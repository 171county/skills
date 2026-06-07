---
name: ai-discovery-expert
description: Expert-level reference for discovering, evaluating, and synthesizing AI research developments in 2025–2026. Use when asked about frontier model capabilities, benchmark methodology, paper evaluation, multimodal AI, hardware/compute trends, arXiv navigation, model capability elicitation, or tracking the state of the art across AI domains.
---

# AI Discovery Expert

## 1. The Frontier Model Landscape (June 2026)

### Major Model Families

| Provider | Flagship Model | Key Strengths | Context Window |
|---|---|---|---|
| **Anthropic** | Claude Mythos (Opus 4.8) | Reasoning, coding, safety | 200K |
| **OpenAI** | GPT-5 | Broad capability, tool use | 128K |
| **Google DeepMind** | Gemini 2 Ultra | Multimodal, long context | 2M |
| **Meta AI** | LLaMA 4 Scout/Maverick | Open weights, efficiency | 128K–1M |
| **xAI** | Grok 3 | Real-time data, math | 128K |
| **Mistral** | Mistral Large 3 | European, efficient | 128K |
| **Cohere** | Command R+ | Enterprise RAG | 128K |

**Open weights models (mid-2026):**
- **LLaMA 4 Maverick**: 400B MoE, best open-weights reasoning
- **Qwen2.5 72B**: strong multilingual, code-focused
- **DeepSeek-R2**: Chinese frontier reasoning model, open weights
- **Mixtral 8x22B**: production-grade MoE baseline
- **Phi-4**: Microsoft's small but capable (14B) model

### Model Selection Decision Framework

```python
def select_model(task_type: str, context_length: int, budget_sensitivity: str) -> str:
    # Cost tiers: Claude Haiku << Claude Sonnet << Claude Opus
    # Capability: reverse order

    if task_type == "complex_reasoning" and budget_sensitivity == "low":
        return "claude-opus-4-8"  # Best reasoning in 2026
    elif task_type == "coding" and context_length > 100_000:
        return "claude-sonnet-4-6"  # Good balance
    elif task_type == "classification" or budget_sensitivity == "high":
        return "claude-haiku-4-5"  # Cost-optimized
    elif task_type == "multimodal_heavy":
        return "gemini-2-ultra"  # Best multimodal
    elif task_type == "local_deployment":
        return "llama-4-maverick"  # Open weights
```

---

## 2. Benchmark Methodology and Literacy

### Primary Benchmarks (2026)

#### Reasoning and General Intelligence

| Benchmark | Description | Current Leader (Jun 2026) |
|---|---|---|
| **HLE (Humanity's Last Exam)** | 3,000 expert-level questions across 100+ domains | Claude Mythos ~45% |
| **MMLU** | 57-domain multiple choice, undergraduate+ | Near-saturated (>90%) |
| **GPQA Diamond** | Graduate-level science questions | ~80%+ for frontier models |
| **ARC-Challenge** | Grade school science, commonsense | Near-saturated |
| **BIG-Bench Hard** | Hard reasoning tasks | Frontier models >85% |

#### Coding Benchmarks

| Benchmark | Description | Current Leader |
|---|---|---|
| **SWE-Bench Verified** | Real GitHub issues from top Python repos | Claude Mythos 93.9% |
| **HumanEval** | 164 Python functions — nearly saturated at 90%+ | Multiple models |
| **MBPP** | 500 Python programming problems | Near-saturated |
| **LiveCodeBench** | Rolling evaluation on new competitive programming | Frontier models ~60% |
| **APPS** | 10,000 algorithmic problems | Frontier ~70% |

#### Multimodal Benchmarks

| Benchmark | Description | Leader |
|---|---|---|
| **MMMU** | Massive Multi-discipline Multimodal Understanding | GPT-5 / Gemini 2 |
| **MathVista** | Mathematical reasoning with visual content | Frontier ~85% |
| **ChartQA** | Chart understanding and reasoning | Near-saturated |
| **Video-MME** | Video understanding | Gemini 2 (2M context) |
| **BLINK** | Visual perception tasks | Frontier ~80% |

#### Safety Benchmarks

| Benchmark | Description |
|---|---|
| **TruthfulQA** | Measures truthfulness / hallucination rate |
| **BOLD** | Bias assessment across demographics |
| **BBQ** | Bias evaluation in ambiguous contexts |
| **AIR-Bench 2024** | Safety policies across AI regulation categories |

### Benchmark Literacy Rules

**Red flags in benchmark reporting:**
1. **Test set contamination**: model trained on benchmark data. Check: did the model see similar problems during training?
2. **Benchmark saturation**: once >95% accuracy, discriminative power disappears. Old benchmarks are useless signals.
3. **Cherry-picked prompting**: some numbers use extensive prompt engineering not representative of typical use
4. **Distribution mismatch**: benchmark tests one thing; real use case is different
5. **Closed evaluations**: company self-reports numbers without third-party verification

**Questions to ask about any benchmark claim:**
- What evaluation protocol was used? (0-shot, 5-shot, chain-of-thought?)
- Who ran the evaluation? (Self-reported vs. third-party)
- What's the confidence interval / standard error?
- When was the eval run? (Models get updated frequently)
- Is this an established benchmark with fixed test sets?

---

## 3. Research Paper Evaluation Framework

### Assessing an AI Paper's Value

**Tier 1 signal (high confidence):**
- Published in top venue: NeurIPS, ICML, ICLR, ACL, EMNLP, CVPR, ECCV
- Reproducible code released with paper
- Independent replication by multiple groups within weeks
- Ablation studies showing which components drive improvement

**Tier 2 signal (medium confidence):**
- arXiv preprint from established lab (Anthropic, DeepMind, OpenAI, Meta AI)
- Clear problem formulation with explicit assumptions
- Statistically significant results with proper baselines
- Code released but minimal documentation

**Tier 3 signal (skepticism warranted):**
- No code release
- Comparisons only to weak or outdated baselines
- Claims of "state-of-the-art" on obscure benchmarks
- Industry press release before peer review

### Paper Reading Protocol (Fast Track)

**20-minute protocol for a new paper:**
1. **Abstract** (2 min): What's the claim? What benchmark? What improvement?
2. **Figures and tables** (5 min): The story is usually in Figure 1 and Table 1
3. **Related work** (3 min): Where does this fit? What does it supersede?
4. **Method section** (7 min): Is the technique novel? What assumptions?
5. **Limitations section** (2 min): What did the authors acknowledge breaks?
6. **Experiments** (1 min): How realistic are the evaluation conditions?

**Key structural elements to check:**
- **Figure 1**: usually the paper's main idea — understand this first
- **Table 1**: main comparison table — check what baselines are included
- **Ablation table**: which components actually matter
- **Appendix**: often where caveats and failure cases live

---

## 4. arXiv and Research Discovery

### Navigating arXiv Efficiently

**Key arXiv categories:**
- `cs.AI` — Artificial Intelligence (general)
- `cs.CL` — Computation and Language (NLP, LLMs)
- `cs.CV` — Computer Vision
- `cs.LG` — Machine Learning (algorithms, theory)
- `stat.ML` — Statistics / ML intersection
- `cs.RO` — Robotics (agentic physical AI)
- `cs.CR` — Cryptography and Security (AI security)

**Discovery tools and resources:**

| Tool | Use |
|---|---|
| **Papers With Code** (paperswithcode.com) | Benchmarks, SOTA tables, code links |
| **Semantic Scholar** | Citation network analysis |
| **Hugging Face Daily Papers** | Curated daily arXiv highlights |
| **AI Alignment Forum** | Safety-focused research discussion |
| **Latent Space Podcast** | Deep technical interviews |
| **interconnects.ai** | Nathan Lambert's ML commentary |
| **Ahead of AI** | Sebastian Raschka's newsletter |
| **ML Safety Newsletter** | Dan Hendrycks curated |

**arXiv search patterns:**
```
# Find all papers on a specific technique
site:arxiv.org "chain-of-thought" "2026"

# Papers With Code — filter by task + dataset
paperswithcode.com/sota/language-modelling-on-wikitext-103

# Semantic Scholar — citation graph for a key paper
semanticscholar.org/paper/{paper_id}/citations
```

---

## 5. Capability Elicitation

Capability elicitation is the art of extracting a model's best performance on a task. It's the difference between a model's "floor" and "ceiling" capability.

### Prompting Techniques for Maximum Capability

**Chain-of-Thought (CoT):**
```python
# Zero-shot CoT
prompt = f"""
{problem_statement}

Let's think step by step.
"""

# Few-shot CoT (more reliable for hard tasks)
examples = [
    {"problem": example_1, "solution": step_by_step_1},
    {"problem": example_2, "solution": step_by_step_2},
]
```

**Tree of Thoughts (ToT):**
```python
# Generate multiple reasoning paths, evaluate, select best
paths = await asyncio.gather(*[
    llm.generate_reasoning_path(problem) for _ in range(5)
])
evaluation_scores = [await llm.evaluate_path(p) for p in paths]
best_path = paths[argmax(evaluation_scores)]
```

**Self-Consistency:**
```python
# Generate N solutions, majority vote
solutions = [await llm.solve(problem) for _ in range(10)]
final_answer = majority_vote(solutions)
# Particularly powerful for math and coding tasks
```

**Least-to-Most Prompting:**
```python
# Decompose problem into sub-problems
sub_problems = await llm.decompose(problem)
solutions = []
for sub in sub_problems:
    solution = await llm.solve(sub, context=solutions)
    solutions.append(solution)
final = await llm.synthesize(problem, solutions)
```

**Extended Thinking (Claude-specific):**
```python
# For Opus models with extended thinking
response = anthropic.messages.create(
    model="claude-opus-4-8",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000  # tokens for internal reasoning
    },
    messages=[{"role": "user", "content": hard_problem}]
)
# Claude's thinking process visible in response.content[0].thinking
```

---

## 6. Multimodal AI State of the Art

### Vision-Language Models (VLMs)

**Architecture evolution:**
- **Early VLMs** (2022–2023): CLIP-based encoders, frozen LLM backbone
- **Instruction-tuned VLMs** (2023–2024): LLaVA, InstructBLIP, Flamingo
- **Native multimodal** (2024–2026): Tokens processed natively, no separate vision encoder bottleneck

**Key capabilities landscape (2026):**
| Capability | State of the Art | Limitation |
|---|---|---|
| Image understanding | Near-human on standard scenes | Rare object classes, fine-grained counts |
| Chart/diagram comprehension | >85% on ChartQA | Complex multi-layer charts |
| Document OCR + understanding | >95% on standard docs | Handwriting, degraded images |
| Video understanding | Gemini 2 (2M context) | Very long videos (>2h) |
| Image generation | Imagen 3, DALL·E 4, Flux | Accurate text rendering (improving) |
| Video generation | Sora 2, Runway Gen-4 | Physics consistency, >1 min |

### Embedding Models (2026)

| Model | Dimensions | Context | MTEB Score |
|---|---|---|---|
| `text-embedding-3-large` | 3072 | 8191 | 64.6 |
| `voyage-3-large` | 2048 | 32000 | 65.2 |
| `gemini-embedding-exp` | 768 | 8192 | 66.1 |
| `cohere-embed-v3` | 1024 | 512 | 64.5 |
| `bge-m3` (open) | 1024 | 8192 | 57.0 |
| `e5-mistral-7b` (open) | 4096 | 32768 | 60.1 |

**Embedding selection:**
- **Production API**: Voyage-3-large (best quality, Anthropic-adjacent)
- **Open/self-hosted**: BGE-M3 (multilingual, free)
- **Low latency**: text-embedding-3-small (fast + cheap)

---

## 7. AI Hardware and Compute Landscape

### GPU Landscape (2026)

| GPU | Vendor | VRAM | HBM Bandwidth | TFLOPS (BF16) | Use Case |
|---|---|---|---|---|---|
| **H200 SXM** | NVIDIA | 141GB | 4.8 TB/s | 1,979 | Large model training |
| **B200 SXM** | NVIDIA | 192GB | 8 TB/s | 4,500 | Frontier training |
| **MI300X** | AMD | 192GB | 5.3 TB/s | 1,307 | Competitive inference |
| **TPU v6 (Trillium)** | Google | N/A | High BW | Proprietary | Google internal |
| **H100 SXM** | NVIDIA | 80GB | 3.35 TB/s | 989 | Inference, fine-tuning |
| **A100** | NVIDIA | 80GB | 2 TB/s | 312 | Legacy training/inference |
| **RTX 4090** | NVIDIA | 24GB | 1 TB/s | 165 | Local inference |

**Inference serving (tokens/second benchmarks):**
- vLLM on H100: ~1,000 tok/s for LLaMA 3 70B (batch=32)
- SGLang: ~1,200 tok/s (better RadixAttention cache reuse)
- TensorRT-LLM: ~1,500 tok/s (NVIDIA-optimized, harder setup)

### Memory Requirements for LLM Inference

```python
def estimate_vram(params_billions: float, precision: str = "fp16") -> float:
    """Estimate VRAM in GB for inference (no KV cache)."""
    bytes_per_param = {"fp32": 4, "fp16": 2, "bf16": 2, "int8": 1, "int4": 0.5}
    base_gb = params_billions * 1e9 * bytes_per_param[precision] / 1e9
    # Add ~20% overhead for activations, framework
    return base_gb * 1.2

# Examples:
# LLaMA 3 7B fp16: 7 * 2 * 1.2 = 16.8 GB → fits RTX 4090
# LLaMA 3 70B fp16: 70 * 2 * 1.2 = 168 GB → needs 2× H100 80GB
# LLaMA 3 70B int4: 70 * 0.5 * 1.2 = 42 GB → fits single H100

def estimate_with_kv_cache(
    params_b: float, 
    seq_len: int,
    batch_size: int,
    num_layers: int = 32,
    precision: str = "fp16"
) -> float:
    base = estimate_vram(params_b, precision)
    # KV cache: 2 (K+V) * layers * seq_len * hidden_dim * bytes_per_param * batch
    # Simplified estimate:
    kv_gb = 2 * num_layers * seq_len * 128 * 2 * batch_size / 1e9
    return base + kv_gb
```

### Key Scaling Laws

**Chinchilla scaling law (Hoffmann et al., 2022):**
```
Optimal training tokens = 20 × model_parameters
L(N, D) = E + A/N^α + B/D^β
where N = parameters, D = tokens, E = irreducible entropy
```

**Post-Chinchilla scaling (2024–2026):**
- Most frontier labs now train on **>100× Chinchilla-optimal tokens** (trading compute for inference efficiency)
- LLaMA 3 70B: trained on 15T tokens (vs. ~1.4T Chinchilla-optimal)
- Training on more tokens → smaller models that match larger models at inference
- **Test-time compute scaling** (OpenAI o1, Claude extended thinking): trading inference compute for accuracy

---

## 8. Fine-Tuning and Adaptation Techniques

### LoRA (Low-Rank Adaptation)

The dominant PEFT technique for 2025–2026. Adapts a model by adding low-rank matrices to attention layers:

```python
from peft import LoraConfig, get_peft_model, TaskType
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3-8B")

config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=16,                          # Rank: lower = fewer parameters, faster
    lora_alpha=32,                 # Scaling factor (usually 2× rank)
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
)

peft_model = get_peft_model(model, config)
peft_model.print_trainable_parameters()
# trainable params: 6,815,744 || all params: 8,037,322,752 || trainable%: 0.08%
```

**LoRA hyperparameter guide:**
| Parameter | Small (classification) | Medium (instruction) | Large (reasoning) |
|---|---|---|---|
| `r` (rank) | 4–8 | 16–32 | 64–128 |
| `lora_alpha` | 16 | 32–64 | 128–256 |
| Learning rate | 3e-4 | 2e-4 | 1e-4 |
| Training data | 1K–10K examples | 10K–100K | 100K+ |

### QLoRA (Quantized LoRA)

Enables fine-tuning large models on consumer hardware:

```python
from transformers import BitsAndBytesConfig
import torch

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3-70B",
    quantization_config=bnb_config,
    device_map="auto"
)
# Now fine-tune 70B model on 24GB VRAM consumer GPU
```

### DPO (Direct Preference Optimization)

Aligns models on preference data without RLHF complexity:

```python
from trl import DPOTrainer, DPOConfig

training_args = DPOConfig(
    beta=0.1,                  # KL penalty strength
    per_device_train_batch_size=2,
    gradient_accumulation_steps=8,
    learning_rate=5e-7,        # Low LR for DPO
    num_train_epochs=3,
    max_length=2048,
    max_prompt_length=512,
)

# Preference dataset format
dataset = [
    {
        "prompt": "Explain quantum entanglement simply",
        "chosen": "Great explanation with clear analogy...",
        "rejected": "Technically correct but confusing response..."
    }
]

trainer = DPOTrainer(
    model=model,
    ref_model=None,  # Optional reference model
    args=training_args,
    train_dataset=dataset,
)
trainer.train()
```

---

## 9. Evaluation and Model Assessment

### RAGAS (RAG Evaluation)

```python
from ragas import evaluate
from ragas.metrics import (
    answer_relevancy,
    faithfulness,      # Is the answer grounded in retrieved context?
    context_recall,    # Does context contain the correct answer?
    context_precision, # Is the context relevant?
)
from datasets import Dataset

eval_dataset = Dataset.from_dict({
    "question": questions,
    "answer": model_answers,
    "contexts": retrieved_contexts,
    "ground_truth": ground_truth_answers,
})

results = evaluate(
    eval_dataset,
    metrics=[answer_relevancy, faithfulness, context_recall, context_precision],
)
print(results)
# {'answer_relevancy': 0.82, 'faithfulness': 0.91, 'context_recall': 0.76, 'context_precision': 0.84}
```

### LLM-as-Judge

```python
JUDGE_PROMPT = """
You are an expert evaluator. Score the following response on a scale of 1-5.

Question: {question}
Response: {response}
Reference Answer: {reference}

Evaluate on:
- Accuracy (1-5): Does the response correctly answer the question?
- Completeness (1-5): Does it cover all aspects of the question?
- Clarity (1-5): Is it well-written and easy to understand?

Respond in JSON: {{"accuracy": X, "completeness": X, "clarity": X, "reasoning": "..."}}
"""

async def judge_response(question, response, reference):
    result = await anthropic.messages.create(
        model="claude-opus-4-8",
        max_tokens=512,
        messages=[{
            "role": "user", 
            "content": JUDGE_PROMPT.format(
                question=question, response=response, reference=reference
            )
        }]
    )
    return json.loads(result.content[0].text)
```

---

## 10. Emerging Research Areas (2025–2026)

### Active Research Frontiers

**Long-context reasoning:**
- Gemini 2 demonstrated 2M token context with needle-in-haystack retrieval
- Key challenge: reasoning quality degradation in the middle of long contexts ("lost in the middle")
- Approaches: position interpolation, ALiBi, YaRN scaling

**Test-time compute scaling:**
- OpenAI o1/o3, Claude extended thinking, Gemini Thinking
- Trading inference compute for accuracy on hard reasoning tasks
- MCTS, beam search over reasoning traces
- **Key insight**: scaling laws apply to inference compute too

**Multimodal frontier:**
- Native multimodal training (not post-hoc vision adapters)
- Audio + video + text as first-class modalities
- World models: models that simulate physical environments

**AI agents and tool use:**
- Computer use / GUI agents (Claude Computer Use, Operator)
- Multi-agent coordination (A2A Protocol, MCP)
- Long-horizon task completion (days-scale agents)

**Mechanistic interpretability:**
- Understanding transformer circuits (Anthropic Interpretability team)
- Sparse autoencoders for feature extraction
- Growing from toy models to production scale

**Alignment and safety:**
- Constitutional AI / RLAIF (scalable oversight)
- Debate as alignment technique
- Activation steering
- Model organisms of misalignment

---

## 11. Discovery Workflow

### Weekly AI Research Routine

```
Monday: 
  - Check Hugging Face Daily Papers (top 5)
  - Scan Ahead of AI newsletter
  - Review Papers With Code SOTA updates

Wednesday:
  - Read 1–2 papers from the week in depth
  - Check Twitter/X: @_akhaliq, @goodfellow_ian, @AnthropicAI, @GoogleDeepMind
  - Review new model releases and HF leaderboard changes

Friday:
  - Synthesize: what changed this week that matters for your work?
  - Update internal knowledge base / Obsidian
  - Share 1 insight with team via Slack
```

### Evaluating a New Model Release

**Systematic checklist for any new model announcement:**
1. [ ] What benchmarks? (MMLU, HLE, SWE-Bench, etc.)
2. [ ] Were evals run third-party or self-reported?
3. [ ] What's the context window? Effective retrieval length?
4. [ ] Pricing vs. capability tradeoff vs. existing models
5. [ ] Available fine-tuning support?
6. [ ] Safety card / model card published?
7. [ ] Code / weights released (open) or API-only?
8. [ ] Any known failure modes in the announcement?
9. [ ] Independent red-teaming results?
10. [ ] Latency benchmarks (time to first token, tokens/sec)?
