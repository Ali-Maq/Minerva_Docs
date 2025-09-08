Based on the official documentation, here's a comprehensive breakdown of CURE-Bench Track 1 challenge:

## Phase 1: Problem Understanding & Requirements

### **Core Challenge Definition**
CURE-Bench Track 1 (Internal Reasoning) evaluates AI models' ability to perform **structured clinical reasoning** without external tools. The challenge tests precision therapeutics decision-making across:

- **Drug recommendation and selection**
- **Treatment safety and efficacy assessment** 
- **Clinical treatment plan design**
- **Drug repurposing identification**
- **Complex patient-disease-drug relationship reasoning**

### **Three Distinct Question Types**

#### **1. Multiple Choice (multi_choice)**
```
Format: Question + Options A-E
Input: Question text + All options provided
Output: Single letter (A, B, C, D, E)
Constraint: Standard MC reasoning
```

#### **2. Open-Ended Multi-Choice (open_ended_multi_choice)**
```
Format: Question + Options A-E (two-step process)
Step 1 Input: Question text ONLY (no options shown)
Step 1 Output: Free-form clinical reasoning
Step 2 Input: Step 1 answer + options + mapping prompt
Step 2 Output: Single letter (A, B, C, D, E) or "None"
Critical Rule: CANNOT show options in initial prompt
```

#### **3. Open-Ended (open_ended)**
```
Format: Question only
Input: Question text
Output: Comprehensive free-form answer
Evaluation: Internal scoring (not public accuracy)
```

### **Reasoning Trace Requirements**
- **Complete conversation history** must be logged
- **All model interactions** recorded in JSON format
- **Qualification review** checks reasoning process
- **Two-step process verification** for open-ended MC questions

### **Data Constraints & Rules**
- **External data allowed** if "reasonably accessible" and "minimal cost"
- **Proprietary datasets** exceeding competition prize value = prohibited
- **Small subscription fees** (e.g., Gemini Advanced) = acceptable
- **Open source models** must use OSI-approved licenses
- **Automated ML tools** permitted with proper licensing

## Phase 2: System Architecture & Evaluation Metrics

### **System Architecture Diagram**

```ascii
┌─────────────────────────────────────────────────────────────────┐
│                        CURE-BENCH TRACK 1                      │
│                    (Internal Reasoning Only)                   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      INPUT PROCESSING                          │
├─────────────────────────────────────────────────────────────────┤
│  Dataset: curebench_testset_phase1.jsonl (2079 questions)      │
│  ┌─────────────┐ ┌─────────────────┐ ┌─────────────────────┐   │
│  │multi_choice │ │open_ended_multi │ │   open_ended        │   │
│  │   (std MC)  │ │    _choice      │ │  (free form)        │   │
│  │             │ │  (2-step proc)  │ │                     │   │
│  └─────────────┘ └─────────────────┘ └─────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PROMPT ENGINEERING                          │
├─────────────────────────────────────────────────────────────────┤
│  Multi-Choice:                                                 │
│  "Question: {question}\nA: {opt_a}\n...\nAnswer:"             │
│                                                                 │
│  Open-Ended MC (Step 1):                                      │
│  "Question: {question}\nAnswer:" (NO OPTIONS SHOWN!)          │
│                                                                 │
│  Open-Ended MC (Step 2):                                      │
│  "Convert answer to A-E:\nQuestion: {q}\nA:{a}...\nAgent: {r}"│
│                                                                 │
│  Open-Ended:                                                   │
│  "Provide comprehensive answer:\nQuestion: {question}"         │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      MODEL INFERENCE                           │
├─────────────────────────────────────────────────────────────────┤
│              ┌─────────────────────────────┐                   │
│              │     YOUR MODEL              │                   │
│              │                             │                   │
│              │  - No External Tools        │                   │
│              │  - No Web Search            │                   │
│              │  - No Database Access       │                   │
│              │  - Pure Internal Reasoning  │                   │
│              │                             │                   │
│              │  Allowed:                   │                   │
│              │  - Pretrained Knowledge     │                   │
│              │  - Mathematical Computation │                   │
│              │  - Logical Reasoning        │                   │
│              └─────────────────────────────┘                   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    RESPONSE PROCESSING                         │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐   │
│  │   Extract       │ │    Validate     │ │     Format      │   │
│  │   Answer        │ │    Response     │ │    Output       │   │
│  │                 │ │                 │ │                 │   │
│  │ • Letter A-E    │ │ • Length check  │ │ • JSON trace    │   │
│  │ • Full text     │ │ • Format check  │ │ • CSV row       │   │
│  │ • Reasoning     │ │ • Null handling │ │ • Metadata      │   │
│  └─────────────────┘ └─────────────────┘ └─────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      OUTPUT FORMAT                             │
├─────────────────────────────────────────────────────────────────┤
│  submission.csv:                                               │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ id,prediction,choice,reasoning                            │ │
│  │ qHBAJ2T5cs5U,"The answer is C...",C,"[{\"role\":\"user\"..│ │
│  │ dYtEF1FnUwSQ,"Option B is correct",B,"[{\"role\":\"user\".│ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                 │
│  meta_data.json:                                               │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ {                                                         │ │
│  │   "model_name": "MedGemma-27B",                          │ │
│  │   "track": "internal_reasoning",                         │ │
│  │   "base_model_type": "OpenWeighted",                     │ │
│  │   "dataset": "cure_bench_phase_1"                        │ │
│  │ }                                                         │ │
│  └───────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

### **Evaluation Metrics & Scoring**

#### **Primary Metrics**
1. **Accuracy (Multi-Choice + Open-Ended MC)**
   - Exact match between predicted choice (A-E) and ground truth
   - Open-ended questions excluded from public accuracy
   - Weighted scoring across question types

2. **Reasoning Quality Assessment**
   - Manual review of reasoning traces
   - Verification of two-step process compliance
   - Clinical reasoning soundness evaluation

#### **Submission Requirements**
```
Required Files:
├── submission.csv (exactly 2079 rows + header)
│   ├── id: Question identifier
│   ├── prediction: Full model response text  
│   ├── choice: A/B/C/D/E or "NOTAVALUE"
│   └── reasoning: JSON conversation history
└── meta_data.json
    ├── model_name: Display name
    ├── track: "internal_reasoning" 
    ├── base_model_type: "API" or "OpenWeighted"
    ├── base_model_name: Actual model identifier
    └── dataset: "cure_bench_phase_1"
```

#### **Critical Validation Rules**
- **No NULL values** in any CSV column (use "NOTAVALUE" for choice)
- **Exact column names**: `id,prediction,choice,reasoning`
- **JSON-formatted reasoning** traces required
- **Two-step process evidence** for open-ended MC questions
- **5 submissions per day** limit, 2 final selections

#### **Disqualification Triggers**
- Using options in open-ended MC initial prompts
- External tool usage (web search, databases, APIs)
- Incomplete or malformed reasoning traces  
- Violating data usage restrictions
- Falsifying model information

The evaluation emphasizes both **accuracy** and **reasoning quality**, with manual qualification review ensuring models follow the clinical reasoning process correctly rather than just pattern matching.


# CURE-Bench Track 1: Model Input/Output Specification

## Input Formats by Question Type

### **Type 1: Multi-Choice (multi_choice)**

**Raw Input Structure:**
```json
{
  "id": "qHBAJ2T5cs5U",
  "question_type": "multi_choice",
  "question": "A 10-year-old child diagnosed with juvenile rheumatoid arthritis (JRA) requires treatment. Genetic testing reveals the child is a poor CYP2C9 metabolizer. Which drug is the most appropriate for this patient?",
  "options": {
    "A": "Celecoxib 200 mg",
    "B": "First Aid Direct Chewable Aspirin", 
    "C": "Florexa",
    "D": "None of the above"
  },
  "correct_answer": "C"
}
```

**Formatted Prompt to Model:**
```
You are a careful clinical reasoning model.
Task: Read the question and options. Think briefly, then output ONLY one letter: A, B, C, D, or E.

Question: A 10-year-old child diagnosed with juvenile rheumatoid arthritis (JRA) requires treatment. Genetic testing reveals the child is a poor CYP2C9 metabolizer. Which drug is the most appropriate for this patient?
A: Celecoxib 200 mg
B: First Aid Direct Chewable Aspirin
C: Florexa
D: None of the above

Answer:
```

### **Type 2: Open-Ended Multi-Choice (open_ended_multi_choice)**

**Raw Input Structure:**
```json
{
  "id": "dYtEF1FnUwSQ", 
  "question_type": "open_ended_multi_choice",
  "question": "A 70-year-old male with familial chylomicronemia syndrome (FCS) presents with memory issues, recurrent pancreatitis, and mild renal impairment (eGFR 60 mL/min). He has no known history of hypersensitivity reactions. What is the most suitable treatment option for this patient?",
  "options": {
    "A": "TRYNGOLZA (Olezarsen Sodium) without dose adjustment",
    "B": "TRYNGOLZA with dose adjustment due to renal impairment", 
    "C": "A triglyceride-lowering drug contraindicated for geriatric patients",
    "D": "A drug targeting APOC-III but requiring pediatric dosing"
  },
  "correct_answer": "B"
}
```

**Step 1 Prompt (NO OPTIONS SHOWN):**
```
You are a careful clinical reasoning model.
Task: Provide a concise, evidence-based answer. Keep the reasoning focused and brief.

Question: A 70-year-old male with familial chylomicronemia syndrome (FCS) presents with memory issues, recurrent pancreatitis, and mild renal impairment (eGFR 60 mL/min). He has no known history of hypersensitivity reactions. What is the most suitable treatment option for this patient?

Answer:
```

**Step 2 Prompt (After getting Step 1 response):**
```
Convert the agent's open-ended answer to a single multiple-choice letter.
Rules: Read the question and options. Consider the agent's answer. Output ONLY one capital letter A–D or the token 'None' if truly not applicable. No extra words.

Question: A 70-year-old male with familial chylomicronemia syndrome (FCS) presents with memory issues, recurrent pancreatitis, and mild renal impairment (eGFR 60 mL/min). He has no known history of hypersensitivity reactions. What is the most suitable treatment option for this patient?
A: TRYNGOLZA (Olezarsen Sodium) without dose adjustment
B: TRYNGOLZA with dose adjustment due to renal impairment
C: A triglyceride-lowering drug contraindicated for geriatric patients
D: A drug targeting APOC-III but requiring pediatric dosing

Agent's answer: [INSERT STEP 1 RESPONSE HERE]

Letter:
```

### **Type 3: Open-Ended (open_ended)**

**Raw Input Structure:**
```json
{
  "id": "IKWfGHlG9aaL",
  "question_type": "open_ended", 
  "question": "Which thyroid hormone replacement therapy is considered both safe and effective for a 32-year-old pregnant woman in her second trimester who has familial papillary thyroid carcinoma and hypothyroidism?",
  "correct_answer": "Levothyroxine is the standard..."
}
```

**Formatted Prompt:**
```
You are a careful clinical reasoning model.
Task: Provide a concise, evidence-based answer. Keep the reasoning focused and brief.

Question: Which thyroid hormone replacement therapy is considered both safe and effective for a 32-year-old pregnant woman in her second trimester who has familial papillary thyroid carcinoma and hypothyroidism?

Answer:
```

## Model Thinking Process Requirements

### **Clinical Reasoning Framework**

**The model should demonstrate:**

1. **Medical Knowledge Integration**
   - Drug mechanisms and interactions
   - Patient-specific factors (age, genetics, comorbidities)
   - Contraindications and safety profiles
   - Dosing considerations

2. **Systematic Clinical Reasoning**
   ```
   Patient Assessment → Drug Selection → Safety Evaluation → Final Recommendation
   ```

3. **Evidence-Based Decision Making**
   - Consider pharmacokinetics/pharmacodynamics
   - Account for genetic factors (e.g., CYP2C9 metabolism)
   - Evaluate risk-benefit ratios
   - Apply clinical guidelines

### **Reasoning Patterns by Question Type**

**Multi-Choice Reasoning:**
```
1. Parse patient demographics and conditions
2. Identify key clinical factors
3. Evaluate each option against patient profile
4. Apply contraindications and safety considerations
5. Select most appropriate option
6. Output single letter
```

**Open-Ended MC Reasoning (Step 1):**
```
1. Comprehensive clinical analysis
2. Consider multiple treatment approaches
3. Provide detailed rationale
4. Explain mechanism of action
5. Address safety and efficacy
6. Give specific recommendation with reasoning
```

**Open-Ended MC Reasoning (Step 2):**
```
1. Review Step 1 comprehensive answer
2. Map reasoning to available options
3. Find best match or determine "None"
4. Output corresponding letter
```

## Expected Output Specifications

### **CSV Row Structure**
```csv
id,prediction,choice,reasoning
```

### **Output by Question Type**

**Multi-Choice Output:**
```csv
qHBAJ2T5cs5U,"C

**Explanation:**

1. **Poor CYP2C9 Metabolizer**: This patient has genetic polymorphisms affecting drug metabolism
2. **Age Consideration**: 10-year-old requires pediatric-appropriate medication
3. **JRA Treatment**: Needs anti-inflammatory therapy
4. **Drug Selection**: Florexa (C) is specifically indicated for pediatric JRA patients with CYP2C9 deficiency

The correct answer is C.",C,"[{""role"": ""user"", ""content"": ""You are a careful clinical reasoning model...[FULL PROMPT]""}, {""role"": ""assistant"", ""content"": ""C\n\n**Explanation:**\n\n1. **Poor CYP2C9 Metabolizer**...""}]"
```

**Open-Ended MC Output:**
```csv
dYtEF1FnUwSQ,"For a 70-year-old male with familial chylomicronemia syndrome (FCS) presenting with memory issues, recurrent pancreatitis, and mild renal impairment (eGFR 60 mL/min), the most suitable treatment would be TRYNGOLZA (Olezarsen Sodium) with dose adjustment due to renal impairment.

**Clinical Rationale:**
1. **FCS Treatment**: TRYNGOLZA targets APOC-III and is FDA-approved for FCS
2. **Renal Impairment**: eGFR 60 mL/min requires dose modification
3. **Age Considerations**: Appropriate for geriatric population
4. **Safety Profile**: No contraindications for this patient
5. **Efficacy**: Addresses underlying triglyceride elevation causing pancreatitis",B,"[{""role"": ""user"", ""content"": ""You are a careful clinical reasoning model. Task: Provide a concise, evidence-based answer...""}, {""role"": ""assistant"", ""content"": ""For a 70-year-old male with familial chylomicronemia syndrome...""}, {""role"": ""user"", ""content"": ""Convert the agent's open-ended answer to a single multiple-choice letter...""}, {""role"": ""assistant"", ""content"": ""B""}]"
```

**Open-Ended Output:**
```csv
IKWfGHlG9aaL,"For a 32-year-old pregnant woman in her second trimester with familial papillary thyroid carcinoma and hypothyroidism, **Levothyroxine (L-T4)** is the most appropriate thyroid hormone replacement therapy.

**Clinical Justification:**
1. **Pregnancy Safety**: FDA Category A drug, extensively studied in pregnancy
2. **Maternal Hypothyroidism**: Essential for fetal neurological development
3. **Cancer Considerations**: TSH suppression may be beneficial for thyroid cancer
4. **Second Trimester**: Critical period for fetal brain development
5. **Dosing**: May require 25-50% dose increase during pregnancy
6. **Monitoring**: Regular TSH and Free T4 monitoring recommended

**Alternative Considerations:**
- Avoid combination T4/T3 preparations during pregnancy
- Natural thyroid extracts not recommended
- Synthetic levothyroxine provides consistent, predictable hormone levels",NOTAVALUE,"[{""role"": ""user"", ""content"": ""You are a careful clinical reasoning model. Task: Provide a concise, evidence-based answer...""}, {""role"": ""assistant"", ""content"": ""For a 32-year-old pregnant woman in her second trimester...""}]"
```

### **Critical Output Requirements**

**Choice Field Rules:**
- Multi-choice: Must be A, B, C, D, or E
- Open-ended MC: Must be A, B, C, D, E, or "None" 
- Open-ended: Must be "NOTAVALUE"
- Never NULL, empty string, or undefined

**Prediction Field Rules:**
- Contains full model response text
- Should demonstrate clinical reasoning
- Must be non-empty string
- Can include formatting (bold, bullets, etc.)

**Reasoning Field Rules:**
- JSON array of conversation messages
- Each message has "role" and "content" fields
- Must capture complete interaction history
- For open-ended MC: includes both steps
- Example format:
```json
[
  {"role": "user", "content": "prompt text"},
  {"role": "assistant", "content": "response text"}
]
```

**Metadata Requirements:**
```json
{
  "meta_data": {
    "model_name": "Your Model Display Name",
    "model_type": "API|Local|Custom", 
    "track": "internal_reasoning",
    "base_model_type": "API|OpenWeighted",
    "base_model_name": "actual-model-identifier",
    "dataset": "cure_bench_phase_1",
    "additional_info": "Any relevant details",
    "average_tokens_per_question": 150.5,
    "average_tools_per_question": 0,
    "tool_category_coverage": ""
  }
}
```

The model must balance comprehensive clinical reasoning with concise, actionable outputs while maintaining strict adherence to the format requirements for each question type.


