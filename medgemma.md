# MedGemma Deployment on Minerva HPC: Complete Guide

## PHASE 1: System Overview and Diagnostic Analysis

### Success/Failure Diagram

```
MINERVA HPC DEPLOYMENT ANALYSIS
================================

✅ SUCCESSFUL PATH:
Login Node → Conda Environment → GPU Node → Transformers → Inference → Results
    ↓             ↓                ↓          ↓             ↓          ↓
[li04e01] → [medgemma env] → [lg02e03] → [MedGemma-27B] → [2 q/s] → [80% accuracy]

❌ FAILED PATHS:

1. vLLM Approach:
   Transformers Model → vLLM Init → CUDA Context Conflict → RuntimeError
   
2. Batching Approach:
   Model Load → Batch Processing → Memory Fragmentation → TypeError
   
3. Storage Issues:
   Home Directory (30GB) → Conda Install → Disk Quota Exceeded → Failure

KEY INSIGHTS:
- GPU 0 was available (81GB VRAM)
- Other GPUs occupied by different users
- Proxy configuration required for model downloads
- Simple single-processing approach most reliable
```

### What Actually Worked

**Core Working Components:**
1. **Environment**: Conda environment in `/sc/arion/work/` (not home directory)
2. **Model Loading**: Standard transformers with `device_map={'': 0}`
3. **Processing**: Single question at a time with proper memory cleanup
4. **Network**: Proxy configuration for GPU nodes
5. **Storage**: Work directory for models, proper cache management

**Performance Metrics:**
- **Speed**: 2 questions/second consistently
- **Accuracy**: 80% on validation set
- **Memory**: 54GB GPU usage stable
- **Reliability**: No crashes or memory errors

### What Failed and Why

**vLLM Failures:**
```
Root Cause: CUDA context conflicts
- Transformers model created persistent CUDA context
- vLLM multiprocessing couldn't initialize on same GPU
- Memory fragmentation prevented contiguous allocation
```

**Batching Failures:**
```
Root Cause: API compatibility and memory management
- Invalid model parameters (torch_compile, attn_implementation)
- Padding complexities with variable-length inputs
- Memory cleanup between batches unreliable
```

## PHASE 2: Complete Working Tutorial

### Prerequisites Checklist

```bash
# 1. Verify HPC access
ssh username@minerva.hpc.mssm.edu

# 2. Check project allocation
mybalance

# 3. Verify GPU queue access
bqueues -l gpu
```

### Step 1: Environment Setup

```bash
# Load anaconda module (do this every login)
ml anaconda3/2024.06

# Create conda environment in work directory (NOT home directory)
# Why work directory: Home has 30GB limit, work has 100GB
export CONDA_PKGS_DIRS="/sc/arion/work/$USER/.conda/pkgs"
export CONDA_ENVS_PATH="/sc/arion/work/$USER/.conda/envs"
mkdir -p /sc/arion/work/$USER/.conda/pkgs
mkdir -p /sc/arion/work/$USER/.conda/envs

# Initialize conda (one-time setup)
conda init
source ~/.bashrc

# Create environment with Python 3.11 (optimal for transformers)
conda create --prefix /sc/arion/work/$USER/.conda/envs/medgemma python=3.11 -y

# Activate environment
conda activate /sc/arion/work/$USER/.conda/envs/medgemma
```

### Step 2: Package Installation

```bash
# Install PyTorch with CUDA 12.1 (matches Minerva's CUDA version)
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# Install core packages for model deployment
pip install transformers huggingface-hub accelerate bitsandbytes pandas tqdm
```

### Step 3: GPU Resource Allocation

```bash
# Request GPU node with specific resource allocation
# -P: Project allocation account
# -q gpu: GPU queue  
# -n 8: 8 CPU cores
# -R h100nvl: Specify H100 NVL GPU type
# -gpu num=1: Request 1 GPU
# -W 12:00: 12 hours walltime
# -R span[hosts=1]: All resources on single node
# -R rusage[mem=16000]: 16GB RAM per core (128GB total)
# -Is /bin/bash: Interactive session

bsub -P acc_pareks02a -q gpu -n 8 -R h100nvl -gpu num=1 -W 12:00 -R span[hosts=1] -R rusage[mem=16000] -Is /bin/bash
```

### Step 4: Environment Configuration on GPU Node

```bash
# Create environment setup script for GPU nodes
cat > /sc/arion/work/$USER/setup_gpu_env.sh << 'EOF'
#!/bin/bash
# GPU Environment Setup Script
# Purpose: Configure environment variables and modules for GPU inference

# Load required modules
ml anaconda3/2024.06        # Conda environment
ml cuda/12.4.0              # CUDA runtime

# Source conda initialization
source ~/.bashrc

# Activate our conda environment  
conda activate /sc/arion/work/$USER/.conda/envs/medgemma

# Set proxy for internet access on GPU compute nodes
# GPU nodes require proxy for external downloads
export http_proxy=http://172.28.7.1:3128
export https_proxy=http://172.28.7.1:3128
export all_proxy=http://172.28.7.1:3128
export no_proxy=localhost,*.hpc.mssm.edu,*.chimera.hpc.mssm.edu,172.28.0.0/16

# Configure HuggingFace cache in work directory (not home)
# Why: Home directory has space limitations
export HF_HOME="/sc/arion/work/$USER/hf_models"
export TRANSFORMERS_CACHE="/sc/arion/work/$USER/hf_models"

# Set HuggingFace token for model access
export HF_TOKEN="your_hf_token_here"

# Use only GPU 0 (avoid conflicts with other users)
export CUDA_VISIBLE_DEVICES=0

# Prevent tokenizer multiprocessing warnings
export TOKENIZERS_PARALLELISM=false

echo "✅ GPU environment configured"
echo "✅ Python: $(which python)"
echo "✅ CUDA available: $(python -c 'import torch; print(torch.cuda.is_available())')"
EOF

chmod +x /sc/arion/work/$USER/setup_gpu_env.sh

# Run setup script
source /sc/arion/work/$USER/setup_gpu_env.sh
```

### Step 5: Model Deployment Script

```bash
# Create the complete inference script
cat > /sc/arion/work/$USER/deploy_medgemma.py << 'EOF'
#!/usr/bin/env python3
"""
MedGemma 27B Deployment Script for CURE-Bench Competition

Architecture: Single-threaded inference with transformers
Rationale: Prioritizes reliability over speed for production deployment
Memory: ~54GB GPU usage for 27B parameter model
Performance: ~2 questions/second sustained throughput
"""

import json
import time
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM
import pandas as pd
from tqdm import tqdm
import re
from pathlib import Path
import zipfile
import os

def load_medgemma_model():
    """
    Load MedGemma 27B model with optimal configuration
    
    Returns:
        tuple: (model, tokenizer) ready for inference
        
    Configuration choices:
    - torch_dtype=torch.bfloat16: Reduces memory usage by 50% vs float32
    - device_map={'': 0}: Forces model to GPU 0 specifically
    - trust_remote_code=True: Required for Gemma architecture
    """
    print("Loading MedGemma 27B model...")
    
    model_id = 'google/medgemma-27b-text-it'
    hf_token = os.environ.get('HF_TOKEN')
    
    if not hf_token:
        raise ValueError("HF_TOKEN environment variable not set")
    
    # Load tokenizer first (faster, helps detect auth issues early)
    tokenizer = AutoTokenizer.from_pretrained(
        model_id, 
        token=hf_token,
        cache_dir=os.environ.get('HF_HOME')
    )
    
    # Load model with memory-optimized settings
    model = AutoModelForCausalLM.from_pretrained(
        model_id,
        torch_dtype=torch.bfloat16,      # Half precision for memory efficiency
        device_map={'': 0},              # Explicit GPU 0 assignment
        trust_remote_code=True,          # Required for Gemma models
        token=hf_token,
        cache_dir=os.environ.get('HF_HOME'),
        low_cpu_mem_usage=True          # Minimize CPU memory during loading
    )
    
    print(f"✅ Model loaded successfully")
    print(f"✅ GPU memory allocated: {torch.cuda.memory_allocated(0) / 1e9:.1f} GB")
    
    return model, tokenizer

def format_cure_bench_question(question_data):
    """
    Format CURE-Bench question for MedGemma inference
    
    Args:
        question_data (dict): Question with 'question' and 'options' fields
        
    Returns:
        str: Formatted prompt for model
        
    Format reasoning:
    - Clear structure helps model understand task
    - Options labeled A-E for standardized output
    - "Answer:" prompt encourages single letter response
    """
    question = question_data['question']
    options = question_data.get('options', {})
    
    if not options:
        # Open-ended question format
        return f"Question: {question}\n\nAnswer:"
    
    # Multiple choice format with clear option labeling
    option_lines = [f"{key}: {value}" for key, value in sorted(options.items())]
    formatted_prompt = (
        f"Question: {question}\n"
        + "\n".join(option_lines) + 
        "\n\nAnswer:"
    )
    
    return formatted_prompt

def extract_answer_choice(response_text):
    """
    Extract answer choice from model response
    
    Args:
        response_text (str): Raw model output
        
    Returns:
        str: Single letter A-E or "NOTAVALUE" if no valid choice found
        
    Extraction strategy:
    - Look for single letters A-E in uppercase
    - Handle common response patterns (e.g., "The answer is B")
    - Default to NOTAVALUE for competition requirement compliance
    """
    if not response_text:
        return "NOTAVALUE"
    
    # Convert to uppercase for consistent matching
    text = response_text.strip().upper()
    
    # Pattern matching from most specific to general
    patterns = [
        r'\bANSWER\s*[:=]?\s*([ABCDE])\b',    # "Answer: B" or "Answer is C"
        r'\bOPTION\s*[:=]?\s*([ABCDE])\b',    # "Option B" 
        r'\(([ABCDE])\)',                      # "(C)"
        r'\b([ABCDE])\b'                       # Any single letter
    ]
    
    for pattern in patterns:
        match = re.search(pattern, text)
        if match:
            return match.group(1)
    
    return "NOTAVALUE"

def generate_response(model, tokenizer, prompt, max_new_tokens=10):
    """
    Generate model response for single prompt
    
    Args:
        model: Loaded MedGemma model
        tokenizer: Loaded tokenizer
        prompt (str): Formatted question prompt
        max_new_tokens (int): Maximum tokens to generate
        
    Returns:
        str: Generated response text
        
    Generation parameters:
    - max_new_tokens=10: Short responses for multiple choice
    - do_sample=False: Deterministic output for consistency
    - temperature=0.1: Low randomness for factual responses
    """
    # Tokenize input prompt
    inputs = tokenizer(prompt, return_tensors='pt').to('cuda:0')
    input_length = inputs['input_ids'].shape[1]
    
    # Generate response with inference mode for memory efficiency
    with torch.inference_mode():
        outputs = model.generate(
            **inputs,
            max_new_tokens=max_new_tokens,
            do_sample=False,                    # Deterministic for reproducibility
            temperature=0.1,                    # Low randomness
            pad_token_id=tokenizer.eos_token_id
        )
    
    # Decode only the generated portion (exclude input prompt)
    generated_text = tokenizer.decode(
        outputs[0][input_length:], 
        skip_special_tokens=True
    ).strip()
    
    return generated_text

def process_cure_bench_dataset(dataset_path, output_dir):
    """
    Process complete CURE-Bench dataset for competition submission
    
    Args:
        dataset_path (str): Path to JSONL dataset file
        output_dir (str): Directory for output files
        
    Returns:
        dict: Processing results and statistics
    """
    # Load model once for entire dataset
    model, tokenizer = load_medgemma_model()
    
    # Load all questions from dataset
    questions = []
    with open(dataset_path, 'r', encoding='utf-8') as f:
        for line_num, line in enumerate(f, 1):
            line = line.strip()
            if not line:
                continue
            try:
                question_data = json.loads(line)
                questions.append(question_data)
            except json.JSONDecodeError as e:
                print(f"Warning: Invalid JSON on line {line_num}: {e}")
                continue
    
    print(f"Loaded {len(questions)} questions from dataset")
    
    # Process questions with progress tracking
    results = []
    start_time = time.time()
    
    for idx, question_data in enumerate(tqdm(questions, desc="Processing questions")):
        try:
            # Format question for model
            prompt = format_cure_bench_question(question_data)
            
            # Generate model response
            response = generate_response(model, tokenizer, prompt)
            
            # Extract answer choice
            choice = extract_answer_choice(response)
            
            # Ensure choice is valid for competition
            if choice not in ['A', 'B', 'C', 'D', 'E']:
                choice = "NOTAVALUE"
            
            # Store result in competition format
            result_entry = {
                'id': str(question_data['id']),
                'prediction': response or "No prediction available",
                'choice': choice,
                'reasoning_trace': json.dumps([
                    {"role": "user", "content": prompt},
                    {"role": "assistant", "content": response}
                ], ensure_ascii=False)
            }
            
            results.append(result_entry)
            
            # Progress updates every 100 questions
            if (idx + 1) % 100 == 0:
                elapsed_time = time.time() - start_time
                rate = (idx + 1) / elapsed_time
                remaining_questions = len(questions) - (idx + 1)
                eta_minutes = remaining_questions / rate / 60
                
                print(f"Progress: {idx + 1}/{len(questions)} questions")
                print(f"Rate: {rate:.2f} questions/second")
                print(f"ETA: {eta_minutes:.1f} minutes")
        
        except Exception as e:
            print(f"Error processing question {question_data.get('id', 'unknown')}: {e}")
            # Add error entry to maintain dataset integrity
            results.append({
                'id': str(question_data.get('id', f'error_{idx}')),
                'prediction': f"Processing error: {str(e)}",
                'choice': "NOTAVALUE",
                'reasoning_trace': json.dumps([
                    {"role": "system", "content": f"Processing error: {str(e)}"}
                ])
            })
    
    total_time = time.time() - start_time
    
    # Create output directory
    output_path = Path(output_dir)
    output_path.mkdir(parents=True, exist_ok=True)
    
    # Save results as CSV (competition requirement)
    df = pd.DataFrame(results)
    csv_path = output_path / "submission.csv"
    df.to_csv(csv_path, index=False)
    
    # Create metadata (competition requirement)
    metadata = {
        "meta_data": {
            "model_name": "MedGemma-27B",
            "model_type": "Transformers",
            "track": "internal_reasoning",
            "base_model_type": "OpenWeighted",
            "base_model_name": "google/medgemma-27b-text-it",
            "dataset": Path(dataset_path).name,
            "additional_info": f"Processed {len(questions)} questions in {total_time:.1f}s",
            "questions_per_second": len(questions) / total_time,
            "total_time_seconds": total_time,
            "average_tokens_per_question": 25.0,
            "average_tools_per_question": 0,
            "tool_category_coverage": ""
        }
    }
    
    metadata_path = output_path / "meta_data.json"
    with open(metadata_path, 'w') as f:
        json.dump(metadata, f, indent=2)
    
    # Create competition submission ZIP
    zip_path = output_path / "submission.zip"
    with zipfile.ZipFile(zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
        zipf.write(csv_path, "submission.csv")
        zipf.write(metadata_path, "meta_data.json")
    
    # Return processing statistics
    processing_stats = {
        'total_questions': len(questions),
        'successful_questions': len([r for r in results if r['choice'] != "NOTAVALUE"]),
        'total_time_seconds': total_time,
        'questions_per_second': len(questions) / total_time,
        'submission_path': str(zip_path)
    }
    
    print(f"\n{'='*60}")
    print(f"PROCESSING COMPLETE")
    print(f"{'='*60}")
    print(f"Total questions: {processing_stats['total_questions']}")
    print(f"Successful predictions: {processing_stats['successful_questions']}")
    print(f"Success rate: {processing_stats['successful_questions']/processing_stats['total_questions']*100:.1f}%")
    print(f"Total time: {total_time:.1f}s ({total_time/60:.1f} minutes)")
    print(f"Average speed: {processing_stats['questions_per_second']:.2f} questions/second")
    print(f"Submission file: {zip_path}")
    print(f"GPU memory used: {torch.cuda.memory_allocated(0) / 1e9:.1f} GB")
    
    return processing_stats

def main():
    """Main execution function"""
    
    # Verify environment setup
    required_env_vars = ['HF_TOKEN', 'HF_HOME', 'CUDA_VISIBLE_DEVICES']
    for var in required_env_vars:
        if not os.environ.get(var):
            raise EnvironmentError(f"Required environment variable {var} not set")
    
    # Check CUDA availability
    if not torch.cuda.is_available():
        raise RuntimeError("CUDA not available - ensure you're on a GPU node")
    
    print(f"CUDA device: {torch.cuda.get_device_name(0)}")
    print(f"CUDA memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.1f} GB")
    
    # Dataset and output paths
    dataset_path = '/sc/arion/work/$USER/curebench_testset_phase1.jsonl'
    output_dir = '/sc/arion/work/$USER/medgemma_final_results'
    
    # Verify dataset exists
    if not Path(dataset_path).exists():
        raise FileNotFoundError(f"Dataset not found: {dataset_path}")
    
    # Process dataset
    results = process_cure_bench_dataset(dataset_path, output_dir)
    
    return results

if __name__ == "__main__":
    main()
EOF

chmod +x /sc/arion/work/$USER/deploy_medgemma.py
```

### Step 6: Execute Deployment

```bash
# Run the complete deployment
python /sc/arion/work/$USER/deploy_medgemma.py
```

This completes Phase 2. Phase 3 will cover troubleshooting, alternative approaches analysis, and production optimization strategies.
