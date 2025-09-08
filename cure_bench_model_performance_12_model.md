# CURE-Bench Model Performance Analysis (12 Models)

## Overall Performance Summary

| Model | Overall Accuracy | Multi-Choice Accuracy | Open-Ended MC Accuracy | Open-Ended Accuracy | Total Cost | Tokens | Key Insights |
|-------|-----------------|----------------------|------------------------|---------------------|------------|--------|--------------|
| **DeepSeek R1** | **90.0%** | 80.0% | 100.0% | **0.0%** | $0.2279 | 36,520 | Best accuracy, expensive reasoning |
| **Llama 3.1 70B** | **80.0%** | 60.0% | 100.0% | **0.0%** | $0.0128 | 15,838 | Best value proposition |
| **GPT-OSS 120B** | **80.0%** | 60.0% | 100.0% | **0.0%** | $0.0145 | 17,361 | Good reasoning model |
| **Kimi K2** | **80.0%** | 60.0% | 100.0% | **0.0%** | $0.0174 | 10,546 | Most token-efficient |
| **Qwen3 30B A3B** | **80.0%** | 80.0% | 80.0% | **0.0%** | $0.0108 | 21,554 | Strong multi-choice performance |
| **Llama 3.3 70B** | 70.0% | 60.0% | 80.0% | **0.0%** | $0.0133 | 16,508 | Solid baseline |
| **Llama 3.1 8B** | 70.0% | 40.0% | 100.0% | **0.0%** | $0.0028 | 15,531 | Most cost-effective |
| **DeepSeek V3** | 70.0% | 40.0% | 100.0% | **0.0%** | $0.0138 | 17,001 | Balanced option |
| **DeepSeek V3.1** | 70.0% | 80.0% | 100.0% | **0.0%** | $0.0369 | 30,714 | Enhanced reasoning |
| **Mixtral 8x22B** | 70.0% | 40.0% | 100.0% | **0.0%** | $0.0142 | 12,226 | MoE architecture |
| **MedGemma 27B** | 70.0% | 40.0% | 100.0% | **0.0%** | $0.0000 | 26,304 | Medical specialized, local |
| **GLM-4.5-Air** | 60.0% | 20.0% | 100.0% | **0.0%** | $0.0305 | 40,495 | Agent-focused model |

## Critical Observations

### 🚨 **MAJOR ISSUE: Open-Ended Questions Always 0.0%**
- **ALL 12 models** show 0.0% accuracy on open-ended questions
- This indicates a **systematic evaluation problem**, not model performance issue

### 📊 **Multi-Choice Performance Patterns**
- **Range**: 20% - 80% accuracy
- **Top performers**: DeepSeek V3.1 (80%), Qwen3 30B (80%), DeepSeek R1 (80%)
- **Struggling models**: GLM-4.5-Air (20%), several at 40%

### ✅ **Open-Ended Multi-Choice Success**
- **Range**: 80% - 100% accuracy
- **Most models achieve 100%** - shows two-step process works well
- **Only exception**: GLM-4.5-Air (100%), Qwen3 30B (80%)

### 💰 **Cost vs Performance**
- **Most expensive**: DeepSeek R1 ($0.2279) - but highest accuracy
- **Best value**: Llama 3.1 70B ($0.0128) - 80% accuracy
- **Free option**: MedGemma 27B (local) - 70% accuracy

### 🔄 **Token Usage Patterns**
- **Highest**: GLM-4.5-Air (40,495 tokens) - lots of verbose reasoning
- **Most efficient**: Kimi K2 (10,546 tokens) - concise but accurate
- **Reasoning models**: Use 25K-40K tokens vs standard 15K-20K

## Question Type Analysis

### Multi-Choice Questions (5 questions)
**Sample Question**: "Which drug brand name is associated with the treatment of acne?"
- **Challenge**: Direct choice extraction from reasoning
- **Success factors**: Clear "The answer is X" conclusions
- **Failure patterns**: Verbose reasoning without clear final choice

### Open-Ended Multi-Choice (5 questions)  
**Sample Question**: "What should patients do if they experience severe allergic reactions during or after receiving fosaprepitant for injection?"
- **Process**: Open reasoning → Map to choices
- **Success rate**: Very high (80-100%)
- **Why it works**: Two-step process allows full reasoning then mapping

### Open-Ended Questions (5 questions)
**Sample Question**: "Which of the following is a withdrawal symptom that Nicotine helps alleviate?"
- **Current result**: 0.0% across ALL models
- **Issue**: Evaluation methodology problem
- **Note**: These questions actually HAVE correct answers and options!

## Root Cause Analysis

### Why Open-Ended is 0.0%?
1. **Data Format Issue**: Open-ended questions have `correct_answer` and `options` but evaluation treats them as free-form
2. **Evaluation Logic Bug**: Code sets accuracy to 0.0 automatically for open-ended type
3. **Incorrect Classification**: Questions might be mislabeled as "open_ended" when they should be "multi_choice"

### Why Multi-Choice Varies So Much?
1. **Choice extraction difficulty**: Different models have different response patterns
2. **Prompt sensitivity**: Some models need more explicit instructions
3. **Reasoning vs conclusion**: Some models reason well but don't conclude clearly
