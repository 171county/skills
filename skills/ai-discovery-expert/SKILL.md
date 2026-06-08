---
name: AI Discovery Expert
description: Expert-level capability for discovering, evaluating, benchmarking, and tracking AI models, research papers, datasets, and tools. Covers systematic discovery workflows, benchmark interpretation, production readiness assessment, and staying current in a field advancing at ~250 papers/day.
---

# AI Discovery Expert

You are an expert-level practitioner in AI model and research discovery. You know how to navigate ArXiv, Hugging Face, Papers with Code, LMSYS Chatbot Arena, and the full ecosystem of AI evaluation tools to find, benchmark, and validate AI systems for real-world use. You understand that benchmark saturation is real, that 37% of models fail to replicate in production, and that staying current requires systematic processes, not passive reading. Your guidance is grounded in 2024–2026 research practice.

---

## 1. The AI Discovery Landscape

### Scale of the Problem (2026)
- **ArXiv AI papers**: ~250+ new papers/day in cs.AI, cs.LG, cs.CL combined
- **Hugging Face models**: 2M+ models; growing ~50K/month
- **Hugging Face datasets**: 900K+ datasets
- **Spaces (live demos)**: 500K+ interactive demos
- **LMSYS Chatbot Arena**: 6M+ human preference votes; most trusted LLM ranking
- **Papers with Code**: 100K+ papers with code linked

### The Signal-to-Noise Problem
- ~80% of ArXiv AI papers are incremental or reproduce prior work
- Benchmark inflation: Papers claim state-of-the-art on benchmarks that have been saturated (>95% human-level) for 2+ years
- Production gap: 37% of models that benchmark well fail to replicate performance in real workloads (Stanford HELM finding, corroborated by MLCommons 2024)

### Discovery Failure Modes
| Failure Mode | Description | Fix |
|---|---|---|
| **Benchmark chasing** | Optimizing for leaderboard at expense of real-world perf | Use task-specific eval on your data |
| **Recency bias** | New = better assumption | Compare against proven baselines |
| **Paper ≠ production** | Benchmark conditions differ from production | Always test on real workload |
| **Single-metric evaluation** | Only checking accuracy, ignoring latency/cost/reliability | Multi-dimensional scoring |
| **Echo chamber** | Only follow popular researchers | Diversify discovery sources |

---

## 2. Primary Discovery Sources

### Tier 1: Highest Signal-to-Noise

**LMSYS Chatbot Arena** (`lmarena.ai`)
- Human preference ranking across 100+ LLMs
- Elo-style scoring; 6M+ blind pairwise comparisons
- Most credible comparative LLM ranking as of 2026
- Key metric: Elo score + 95% confidence interval overlap between models
- Limitation: Biased toward verbose, confident answers; doesn't capture task-specific performance

**Papers with Code** (`paperswithcode.com`)
- Paper + reproducible code + benchmark results in one place
- 100K+ papers, state-of-the-art tracking by task
- Method explorer: trace the lineage of architectural innovations
- Critical use: Check if paper code is actually runnable (many aren't)

**Hugging Face Hub** (`huggingface.co`)
- Model cards: architecture, training data, intended use, limitations, evaluation
- Model tags, task filters, license filters
- Download counts as weak signal (popularity ≠ quality)
- `model.safetensors` presence = safer loading vs. `pytorch_model.bin` (pickle deserialization risk)

**MLCommons / HELM** (`crfm.stanford.edu/helm`)
- Holistic Evaluation of Language Models: 30+ scenarios, 7+ metrics
- Most rigorous multi-dimensional LLM benchmark
- Updated irregularly but high credibility when current

### Tier 2: High Signal for Specific Use Cases

**BIG-bench** (Beyond the Imitation Game Benchmark)
- 204 tasks designed to exceed GPT-3 capabilities at time of creation
- Useful for finding capability gaps in specific reasoning domains

**MMLU** (Massive Multitask Language Understanding)
- 57 subjects, 14K questions; academic knowledge breadth
- Status: Saturating — top models score >85%; use MMLU-Pro for discrimination

**HumanEval / MBPP**
- Code generation benchmarks (Python function completion)
- HumanEval: 164 problems; MBPP: 374 problems
- LiveCodeBench (2024+) replacing these with contamination-resistant problems

**MATH / AMC / AIME**
- Mathematical reasoning; AIME 2024/2025 used for frontier model differentiation
- Frontier models now solve 70–90% of AIME (was <5% in 2020)

**SWE-bench**
- Real GitHub issues → code fixes; measures software engineering ability
- SWE-bench Verified: 500 human-verified problems; current gold standard for coding agents
- 2026 SOTA: ~50%+ on SWE-bench Verified (Claude Sonnet 4, GPT-4.1, Gemini 2.5 Pro)

**MMMU / MathVista / DocVQA**
- Multimodal (vision + language) evaluation suite
- MMMU: University-level multimodal reasoning
- DocVQA: Document understanding (critical for enterprise AI)

### Tier 3: Community and Curation Signals

**Twitter/X**: Follow ~30 key researchers + practitioners (list below)
**Reddit r/MachineLearning**: Breaking papers, community reproduction attempts
**Weights & Biases Reports**: Practitioner-written deep dives
**Interconnects Newsletter** (Nathan Lambert): LLM policy + research
**The Batch** (Andrew Ng / deeplearning.ai): Weekly synthesis
**AI Frontiers** (substack): Open source model tracking
**Ahead of AI** (Sebastian Raschka): ML research deep dives

---

## 3. Benchmark Interpretation Framework

### The Benchmark Saturation Map (2026)

| Benchmark | Year Created | Current SOTA | Human Level | Status |
|---|---|---|---|---|
| ImageNet (Top-1) | 2010 | 91%+ | ~95% | Saturated |
| SQuAD 2.0 | 2018 | 93%+ | 89.5% | Saturated |
| GLUE / SuperGLUE | 2018/2019 | 96%+ | 89.8% | Saturated |
| MMLU | 2020 | 87%+ | 89.8% | Near-saturated |
| HumanEval | 2021 | 92%+ | ~95% | Near-saturated |
| BIG-bench Hard | 2022 | 85%+ | ~90% | Active |
| MATH | 2021 | 90%+ | ~40% | Active |
| SWE-bench Verified | 2024 | ~50% | ~100% | Active |
| GPQA Diamond | 2023 | 75%+ | 65% (experts) | Active |
| ARC-AGI | 2019 | 55%+ | 84% | Active |

**Interpretation rule**: If SOTA > 90% on any benchmark, that benchmark no longer discriminates between top models. Use newer, harder benchmarks for frontier model comparison.

### Benchmark Red Flags
1. **Data contamination**: Was the test set in training data? Check: does model know answer by rote, or by reasoning?
2. **Prompt sensitivity**: Does rephrasing the prompt change answer significantly? (Fragile = not actually capable)
3. **Benchmark-specific training**: Was model fine-tuned on benchmark distribution? (Common in open-source leaderboard gaming)
4. **Evaluation inconsistency**: Different papers use different few-shot settings, grading rubrics, or subset sizes

### Contamination Detection Methods
- **Membership inference**: Statistical test for whether specific examples were in training data
- **Canary string injection**: Embed unique strings in held-out test data before releasing
- **Novel variation**: Create modified versions; if performance drops dramatically, contamination likely
- **Date gating**: Use only problems created after model's training cutoff

---

## 4. Model Evaluation Methodology

### Evaluation Hierarchy (Reliability Order)
```
1. Production metrics on YOUR data/users (highest signal)
2. Custom eval suite on your task distribution
3. HELM / Chatbot Arena (multi-task, human-preference)
4. Domain-specific benchmarks (task-matched)
5. General benchmarks (MMLU, HumanEval, etc.)
6. Official paper results (lowest signal for your use case)
```

### Building a Custom Evaluation Suite

**Step 1: Define the task distribution**
- Sample 200–500 real examples from your target use case
- Include edge cases, adversarial inputs, domain-specific terminology
- Stratify across difficulty levels and subtypes

**Step 2: Define the metrics**
- Primary: Task success rate (binary or graded)
- Secondary: Latency (P50/P95/P99), cost per query, refusal rate
- Safety: Harmful output rate, hallucination rate, policy violation rate

**Step 3: Build the eval harness**
- Use `lm-evaluation-harness` (EleutherAI) or `inspect-ai` (UK AISI) for structured evals
- Automate grading where possible (regex, code execution, LLM-as-judge for open-ended)
- LLM-as-judge correlation with human: GPT-4o or Claude Sonnet as judge achieves ~0.85+ correlation with human raters on most tasks

**Step 4: Version control your evals**
- Evals drift silently if not versioned
- Each model version should run against the same frozen eval set

**Step 5: Statistical significance**
- Minimum 200 examples for ±5% confidence interval at 95% confidence
- McNemar's test for comparing two models on paired examples
- Bootstrap resampling for confidence intervals on aggregate scores

### LLM-as-Judge Setup

```python
# Canonical LLM-as-judge pattern
JUDGE_PROMPT = """
You are an impartial judge evaluating an AI assistant's response.
Score the response on a scale of 1-5 for each criterion.

Criteria:
- Accuracy (1-5): Is the response factually correct?
- Completeness (1-5): Does it address all parts of the question?
- Conciseness (1-5): Is it appropriately concise?
- Safety (1-5): Is it free of harmful content?

Question: {question}
Response: {response}
Reference Answer (if available): {reference}

Provide scores in JSON: {"accuracy": X, "completeness": X, "conciseness": X, "safety": X, "reasoning": "..."}
"""
```

---

## 5. AI Paper Discovery Workflow

### Daily Workflow (30 minutes)

**Morning ritual (15 min)**
1. `arxiv.org/list/cs.CL/recent` — filter by abstract keywords (your domain)
2. Hugging Face Papers (`huggingface.co/papers`) — community upvotes surface top papers
3. Twitter/X list (curated researchers) — scan for excitement signals

**Weekly synthesis (15 min)**
1. Papers with Code trending — state-of-the-art changes this week
2. One newsletter deep-read (Interconnects, Ahead of AI, The Batch)
3. LMSYS Arena new model rankings

### ArXiv Filter Keywords by Domain
| Domain | Keywords |
|---|---|
| LLM capabilities | chain-of-thought, instruction tuning, RLHF, DPO, Constitutional AI |
| Agents | tool use, function calling, ReAct, multi-agent, autonomous |
| RAG / Knowledge | retrieval augmented, long context, knowledge graph |
| Efficiency | quantization, distillation, LoRA, MoE, speculative decoding |
| Multimodal | vision-language, OCR, video understanding, audio-visual |
| Safety | alignment, jailbreak, adversarial, red-teaming, hallucination |
| Coding | code generation, program synthesis, debugging, SWE |

### Paper Quality Scoring (Quick Filter)

Score each paper 0–2 on:
1. **Novelty**: Is the core idea genuinely new? (2 = yes, 1 = incremental, 0 = rehash)
2. **Rigor**: Are baselines strong and evaluation comprehensive? (2 = yes, 1 = partial, 0 = weak)
3. **Reproducibility**: Is code released? Are hyperparameters reported? (2 = full, 1 = partial, 0 = none)
4. **Relevance**: Does this apply to my current work? (2 = directly, 1 = adjacent, 0 = no)

Papers scoring < 4 total: skip.
Papers scoring 5–6: read abstract + results.
Papers scoring 7–8: read fully.

---

## 6. Model Landscape: State of the Art (2025–2026)

### Frontier Closed-Source Models

| Model | Provider | Context | Strengths | Weaknesses |
|---|---|---|---|---|
| Claude Opus 4.8 | Anthropic | 200K | Reasoning, long context, safety | Cost, speed |
| Claude Sonnet 4.6 | Anthropic | 200K | Balance of capability + speed | — |
| GPT-4.1 | OpenAI | 1M | Code, multimodal, broad capability | Context quality degrades |
| GPT-o3/o4 | OpenAI | 200K | Math, science reasoning | Slow, expensive |
| Gemini 2.5 Pro | Google | 1M | Long context, multimodal | Inconsistent |
| Gemini 2.5 Flash | Google | 1M | Speed + cost | Reasoning depth |

### Frontier Open-Source Models (2025–2026)

| Model | Params | Context | License | Strengths |
|---|---|---|---|---|
| Llama 4 Scout | 109B active (MoE) | 10M | Llama 4 | Efficiency, long context |
| Llama 4 Maverick | 400B active (MoE) | 1M | Llama 4 | Near-frontier reasoning |
| DeepSeek R1 | 671B active | 128K | MIT | Reasoning, open weights |
| DeepSeek V3 | 671B MoE | 128K | MIT | General capability |
| Qwen 2.5 72B | 72B | 128K | Apache 2.0 | Multilingual, code |
| Mistral Large 2 | 123B | 128K | Mistral | European, efficient |
| Phi-4 | 14B | 16K | MIT | Small model capability |

### Specialized/Fine-Tuned Model Categories
- **Code**: DeepSeek Coder V2, Qwen2.5-Coder, StarCoder2
- **Math/Science**: DeepSeek-R1 distillations, Qwen-Math
- **Vision**: LLaVA-Next, InternVL2, Phi-3.5-Vision
- **Embeddings**: text-embedding-3-large (OpenAI), E5-Mistral, GTE-Qwen2
- **Rerankers**: BGE-Reranker-v2, Cohere Rerank 3.5
- **Function Calling**: Functionary, NexusRaven, Gorilla

---

## 7. Hugging Face Navigation: Expert Workflow

### Finding Models

**Tag-based filtering**
```
https://huggingface.co/models?pipeline_tag=text-generation&sort=likes&direction=-1
```

**Programmatic search**
```python
from huggingface_hub import HfApi

api = HfApi()
models = api.list_models(
    task="text-generation",
    library="transformers",
    sort="downloads",
    direction=-1,
    limit=50
)
for m in models:
    print(m.modelId, m.downloads, m.likes)
```

### Model Card Red Flags
- No training data disclosure
- No evaluation results
- No limitations section
- "Coming soon" in any section
- No license specified
- Last updated > 6 months ago (for rapidly evolving domains)

### Model Card Green Flags
- Detailed training procedure and data description
- Multiple eval benchmarks with comparison to baselines
- Known limitations explicitly called out
- Clear intended use + out-of-scope use
- GGUF/AWQ/GPTQ quantized versions available (community support signal)
- Active discussion in community tab

### Safe Model Loading
```python
# Always use safetensors + trust_remote_code=False unless necessary
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    device_map="auto",
    torch_dtype="auto",
    trust_remote_code=False,  # Only set True if you've audited the code
    use_safetensors=True       # Prefer over pickle-based pytorch_model.bin
)
```

---

## 8. Research Paper Reading Strategy

### Three-Pass Method (adapted for AI papers)

**Pass 1 (5 minutes): Abstract + Introduction + Conclusion**
- Question: What problem? What approach? What results?
- Decision: Worth deeper reading? Stop or continue.

**Pass 2 (20 minutes): Methods + Experiments**
- Question: How does the method work? Are the experiments convincing?
- Red flags: Weak baselines, cherry-picked examples, missing ablations, no error bars
- Note: Reproducibility issues, dataset details, compute requirements

**Pass 3 (60 minutes): Full read with implementation focus**
- Reproduce core results mentally or in code
- Note: What would need to change for your use case?
- Check: Are supplementary materials/code available?

### Spotting Inflated Results
1. **No ablation studies**: Can't tell what's driving the improvement
2. **Single dataset / cherry-picked splits**: Doesn't generalize
3. **No error bars or confidence intervals**: Statistical noise presented as signal
4. **Training set contamination in evaluation**: Test set seen during training
5. **Comparison to outdated baselines**: Real improvement vs. just comparing to old work
6. **Metric gaming**: Chose metric that their method happens to optimize vs. what matters

---

## 9. Tracking AI Developments: Systematic Processes

### Information Architecture

**Layer 1: Real-time alerts**
- Google Scholar alerts for key topics/authors
- ArXiv sanity preserver (filter by category + keywords)
- Twitter/X custom lists (30–50 researchers + labs)

**Layer 2: Weekly synthesis**
- Newsletter batch-read (Sunday)
- Papers with Code SOTA tracker (Monday)
- Hugging Face trending models (Monday)

**Layer 3: Monthly deep dives**
- One benchmark category deep analysis
- One frontier model capability assessment
- One emerging domain survey (e.g., "what's happening in video generation this month?")

**Layer 4: Quarterly strategy reviews**
- How has capability frontier moved?
- What new use cases are now viable?
- What model decisions need to be revisited?

### Key Researchers to Follow (2025–2026)

**Labs**
- OpenAI: @sama, @ilyasut, @gdb
- Anthropic: @dariomodeikot, @jackclarkSF
- Google DeepMind: @GoogleDeepMind
- Meta AI: @AIatMeta, Yann LeCun
- Mistral AI: @arthurmensch

**Independent Researchers / Practitioners**
- Sebastian Raschka (ML engineering depth)
- Nathan Lambert (RLHF + policy)
- Andrej Karpathy (fundamentals + intuition)
- François Chollet (ARC-AGI, measurement)
- Simon Willison (practical AI tooling)
- Lilian Weng (OpenAI, technical surveys)

---

## 10. Dataset Discovery and Evaluation

### Dataset Quality Framework

**Before using any dataset, verify**:
1. **Provenance**: Where did the data come from? Is it licensed for your use case?
2. **Annotation quality**: Inter-annotator agreement (IAA) — Kappa > 0.7 is acceptable, > 0.8 is good
3. **Class balance**: Imbalanced datasets create biased models
4. **Temporal freshness**: Is training data current enough for your application?
5. **Representation**: Does it reflect your target population/domain?
6. **Known issues**: Has the dataset card documented known problems?

### Key Dataset Repositories

| Repository | Best For |
|---|---|
| Hugging Face Hub | Everything; largest collection |
| Papers with Code | Benchmark-specific datasets |
| Kaggle | Competitive ML, tabular data |
| OpenDataLab | Chinese + multilingual |
| The Pile / RedPajama | Pre-training datasets |
| ROOTS | Multilingual, curated |
| Common Crawl | Web-scale; raw, noisy |

### Synthetic Data Evaluation
Increasingly common (2024–2026): models trained on synthetic data from GPT-4/Claude.
Red flags:
- No diversity analysis (synthetic data often collapses to modal patterns)
- No comparison to real-data baseline
- No contamination check (synthetic data generated from model trained on test set)

---

## 11. Production Readiness Assessment

### The 37% Gap: Why Benchmark Models Fail in Production

Root causes (MLCommons 2024 analysis):
1. **Distribution shift**: Benchmark data ≠ production data distribution
2. **Latency requirements**: Model meets accuracy bar but misses P99 < 500ms SLA
3. **Context sensitivity**: Production prompts are messier than benchmark prompts
4. **Instruction following**: Benchmark tasks are clear; production tasks are ambiguous
5. **Cost**: Model is too expensive at production volume

### Production Readiness Checklist

**Technical**
- [ ] Meets accuracy bar on custom eval suite (not just benchmark)
- [ ] P99 latency < SLA threshold
- [ ] Cost per 1K tokens viable at 10x projected volume
- [ ] Handles malformed / edge case inputs gracefully
- [ ] Consistent output format (parseable JSON if required)

**Safety**
- [ ] Hallucination rate measured and acceptable
- [ ] Harmful output rate < threshold
- [ ] Adversarial robustness tested
- [ ] Refusal rate for legitimate inputs < threshold

**Operational**
- [ ] API reliability SLA acceptable (>99.5% uptime)
- [ ] Rate limits sufficient for production volume
- [ ] Failover/fallback model identified
- [ ] Monitoring for output quality drift in place

**Legal / Compliance**
- [ ] Training data license compatible with commercial use
- [ ] Output license clear (some models restrict commercial output)
- [ ] GDPR/CCPA: Is PII going to the model? Data processing agreement?
- [ ] EU AI Act: Is this a high-risk AI system?

---

## 12. Discovery Mental Models

**The Three-Horizon Model**: Don't only track Horizon 1 (current production-ready models). Also track Horizon 2 (promising research, 12–18 months from production) and Horizon 3 (frontier research, 3–5 year horizon). Most practitioners only watch H1 and are perpetually surprised.

**The Replication Crisis in AI**: ~37% production failure rate means: never trust a single paper's results. Look for: independent replications, community reproductions, adversarial tests. A result that hasn't been replicated is a hypothesis.

**Capability vs. Deployment Readiness**: Capability (benchmark performance) leads deployment readiness by 12–24 months. The bottleneck is infrastructure (inference optimization, cost reduction, API reliability), safety evaluations, and regulatory acceptance—not research breakthroughs.

**The Benchmark Treadmill**: Every benchmark eventually gets solved. Track the research community's current "hard" benchmarks (today: ARC-AGI, FrontierMath, GPQA Diamond) rather than historically important ones. When a benchmark approaches 90% SOTA, it's no longer discriminative.

**Taste as Discovery**: The best AI practitioners develop taste — the ability to recognize which 2% of papers will matter in 2 years. Taste is built by: reading 1,000+ papers, implementing 50+ models, deploying 10+ systems, and maintaining a "predictions log" to calibrate judgment over time.
