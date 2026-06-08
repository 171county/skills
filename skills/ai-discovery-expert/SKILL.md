---
name: ai-discovery-expert
description: Expert-level guidance on AI model discovery, evaluation, and selection. Use this skill whenever the user asks about evaluating frontier AI models (Claude, GPT, Gemini, Llama, DeepSeek, Mistral), interpreting AI benchmarks (SWE-bench, GPQA, HLE, MMLU, LMSYS Arena, MTEB), tracking state-of-the-art model capabilities, comparing open-source vs. closed models, understanding model architectures (MoE, SSM/Mamba, dense transformer), selecting embedding models, evaluating models for specific tasks (reasoning, coding, vision, long context), understanding model release patterns and capability trends, or building a model discovery and evaluation process for an organization. This skill is essential when the user needs to select the right AI model for a use case, stay current with AI capability improvements, or interpret benchmark results critically.
---

# AI Discovery Expert

You are an expert advisor on AI model evaluation, benchmark interpretation, and model selection. You approach model claims with rigorous skepticism and help practitioners cut through marketing noise to find the right model for specific use cases.

---

## Part 1: The Benchmark Landscape — What Each Measures

### Reasoning and Knowledge Benchmarks

**GPQA Diamond (Graduate-Level Physics/Biology/Chemistry Questions)**
- What it measures: Expert-level reasoning requiring genuine domain knowledge
- Format: 448 multiple-choice questions written by PhD researchers; verified to stump experts without specialized tools
- Why it matters: Resistance to data contamination; requires actual reasoning, not memorization
- Context: GPT-4 initial (~50%); Claude 3 Opus (~53%); Claude 3.5 Sonnet (~59%); Claude Sonnet 4.6 / GPT-4o class: ~65-70%+
- Human expert baseline: ~65% (without access to resources); human with resources: ~81%
- **Interpretation trap**: High GPQA scores don't predict code generation or instruction following quality

**HLE (Humanity's Last Exam) — 2025**
- What it measures: Extreme difficulty multi-domain questions requiring cross-domain reasoning
- Context: Designed to be at the frontier of difficulty; early frontier models scored <5%; leading 2025 models score 15-25%+
- Notable: More resistant to contamination than MMLU due to novel question construction
- Use for: Identifying absolute capability ceiling differences between models

**MMLU (Massive Multitask Language Understanding)**
- What it measures: Knowledge across 57 subjects
- Status: Near-saturated by top models (>90%); poor differentiator in 2025
- Still useful for: Evaluating smaller/cheaper models; baseline comparisons
- **Warning**: High MMLU scores are necessary but not sufficient for production performance

**ARC-Challenge (AI2 Reasoning Challenge)**
- What it measures: Grade 8-12 science questions that require reasoning, not lookup
- Still relevant for: Screening efficient/small models; SLM evaluation

**HellaSwag / WinoGrande / BoolQ**
- Status: Largely saturated for frontier models; useful for benchmarking SLMs and fine-tuned models

### Coding Benchmarks

**SWE-bench Verified**
- What it measures: Ability to resolve real GitHub issues from popular Python repositories (end-to-end software engineering)
- Why it's the gold standard: Real-world tasks, verified solutions, agentically solved
- SOTA scores (mid-2026): Leading models/agents in the 50-65%+ range
- Context: SWE-bench Full is harder; Verified subset removes ambiguous issues
- **Interpretation**: Scores are agent-system scores, not pure model scores — scaffolding and tools matter as much as the model

**HumanEval / MBPP**
- What they measure: Function-level code completion from docstring
- Status: Largely saturated (90%+) for frontier models; useful for SLM screening
- Limitation: Single-function problems don't reflect real-world software engineering

**LiveCodeBench**
- What it measures: Recently released competitive programming problems (Codeforces, LeetCode, etc.)
- Why it matters: Contamination-resistant due to recency
- Most representative of: Competitive programming ability, algorithmic reasoning

**EvalPlus (HumanEval+, MBPP+)**
- Extended versions with more comprehensive test cases; better than original HumanEval for 2025

### Instruction Following and Alignment

**IFEval (Instruction Following Evaluation)**
- What it measures: Can the model follow explicit formatting/structural instructions? (e.g., "respond in exactly 3 bullet points," "don't use the word 'very'")
- Highly practically relevant for production use cases

**MT-Bench / Chatbot Arena (LMSYS)**
- **MT-Bench**: GPT-4 judged; tests multi-turn conversation quality across 8 categories
- **Chatbot Arena (LMSYS)**: Human preference voting; ELO-ranked; blind pairwise comparisons
- Why Chatbot Arena is the most reliable: Aggregates real human preferences across millions of comparisons; resistant to goodhart's law
- **Critical use**: Use LMSYS Arena ELO as the final arbiter of general conversational quality

**AlpacaEval 2 (LC)**
- What it measures: Length-controlled win rate vs. GPT-4 Turbo judged by GPT-4
- Useful for: Comparing instruction-following quality with length bias corrected

### Multi-Modal Benchmarks

**MMMU (Massive Multi-discipline Multimodal Understanding)**
- What it measures: College-level multi-modal questions requiring visual understanding + domain knowledge
- Most relevant for: Vision-language model evaluation

**DocVQA / ChartQA / InfographicVQA**
- What they measure: Document and structured visual understanding
- Highly relevant for: Enterprise document processing, data extraction from visual formats

**MathVista / MathVision**
- What they measure: Mathematical reasoning from visual inputs (diagrams, equations, charts)

### Embedding and Retrieval Benchmarks

**MTEB (Massive Text Embedding Benchmark)**
- What it measures: Embedding quality across 8 tasks: classification, clustering, retrieval, reranking, semantic similarity, summarization, bitext mining, pair classification
- The definitive benchmark for selecting embedding models
- Top 2025 models: OpenAI text-embedding-3-large, Cohere embed-v4, voyage-3, GTE-Qwen2-7B, BGE-M3

**Key MTEB sub-scores**:
- Retrieval (BEIR benchmark average): Most relevant for RAG use cases
- STS (Semantic Textual Similarity): Relevant for deduplication, similarity search

---

## Part 2: Model Architecture Fundamentals

### Dense Transformer (Standard)
The dominant architecture through 2024. All parameters active for every token. Scales predictably with compute per Chinchilla laws.

**Limitations**: Inference cost scales linearly with parameter count; 70B parameters always means 70B parameter compute per forward pass.

### Mixture of Experts (MoE)
Architecture where a routing layer activates only K of N expert FFN layers per token. A 141B-parameter MoE model may use only 22B active parameters per token (Mixtral 8x22B; GPT-4 reported architecture).

**Advantages**:
- More total parameters (capacity) at same inference cost
- Specialized experts can handle different domains differently
- Typically 2-4x more compute-efficient than dense models of same quality

**Production considerations**:
- Memory footprint is for all parameters (141B total), not just active (22B)
- Load balancing across experts is critical; auxiliary loss functions needed during training
- Serving requires specialized infrastructure (Megablocks, DeepSpeed-MoE)

**Notable MoE models**: GPT-4 (reported), Mixtral 8x7B/8x22B, DeepSeek-V3, Grok-1/2

### State Space Models (SSMs) / Mamba

Alternative to attention with linear-time inference (O(n) vs O(n²) for attention).

**Mamba (2023-2024)**: Selective state spaces; context size not limited by quadratic attention cost. Mamba-2 improved performance. Pure Mamba still lags transformers on many language tasks.

**Hybrid architectures (2025 trend)**: Combining SSM layers with attention layers (e.g., Jamba = Mamba + Transformer, Zamba, Griffin). Hybrids often achieve better results than pure SSM while maintaining efficiency gains for long-context tasks.

**When SSMs/hybrids matter**: Very long context (>200K tokens); streaming inference; edge deployment.

### State of Long-Context Models (2025)
- Claude Sonnet 4.6 / Opus: 200K token context window
- Gemini 1.5 Pro/Flash: 1M-2M token context
- GPT-4o: 128K
- Most open-source models: 8K-128K (LLaMA 3.3 supports 128K with RoPE scaling)

**Long-context performance caveat**: Context window ≠ effective context. Most models degrade on retrieval tasks in the middle of long contexts ("lost in the middle" problem). Needle-in-haystack tests measure this; use Ruler benchmark for comprehensive long-context evaluation.

---

## Part 3: Frontier Model Tracking

### Model Family Reference (Current as of mid-2026)

**Anthropic Claude**
- Claude 4.x family: Opus 4.8 (highest capability), Sonnet 4.6 (balanced), Haiku 4.5 (fast/cheap)
- Strengths: Long-form reasoning, code quality, instruction following, safety alignment, document analysis
- Extended thinking / hybrid reasoning: Chain-of-thought as both pre-computation and visible reasoning
- Key feature: Prompt caching (up to 90% cost reduction for repeated prefixes)

**OpenAI GPT / o-series**
- GPT-4o: Strong on multimodal, speed; 128K context
- o3 / o4 reasoning models: Extended compute at inference for complex reasoning tasks
- Strengths: Multi-modal (vision+audio+text), broad ecosystem, function calling maturity

**Google Gemini**
- Gemini 2.5 Pro: Frontier capability, 1M+ context, multimodal
- Gemini 2.5 Flash: Efficient inference, speed/cost optimized
- Strengths: Extreme long context, Google integration, reasoning improvements

**DeepSeek**
- DeepSeek V3 (dense) and R1 (reasoning): Chinese lab producing SOTA open-weight models
- DeepSeek R1: Reasoning model competitive with o1/o3 on many benchmarks; open-weight
- Business impact: Dramatically reduced perceived cost of frontier-class model training; disrupted market pricing

**Meta Llama**
- Llama 3.3 70B: Best open-weight model at this size class
- Llama 3.1/3.2 family: Range from 1B to 405B; Apache 2.0 license; broad hardware support
- Key use case: Self-hosted inference, fine-tuning, cost-sensitive high-volume applications

**Mistral**
- Mistral Large: Frontier-class closed model
- Mistral Nemo / 7B / 8x22B: Open-weight alternatives; Apache 2.0
- Mixtral: MoE architecture; efficient inference with high quality

**Qwen (Alibaba)**
- Qwen 2.5 72B: Top open-weight performance on coding and math; strong multilingual
- Qwen 2.5 Coder: Specialized coding model, competitive with GPT-4 on code benchmarks

---

## Part 4: Model Selection Decision Framework

### Selection Criteria by Use Case

| Use Case | Primary Criterion | Recommended Starting Point |
|---|---|---|
| Complex reasoning / analysis | GPQA / HLE score | Claude Opus 4.8 / GPT-4o / Gemini 2.5 Pro |
| Code generation | SWE-bench / LiveCodeBench | Claude Sonnet 4.6 / GPT-4o / Qwen2.5 Coder |
| High-volume classification | Cost per token, speed | Claude Haiku 4.5 / GPT-4o-mini / Gemini Flash |
| Long document analysis | Effective context length + RULER | Gemini 2.5 Pro / Claude Sonnet (200K) |
| Instruction following | IFEval, LMSYS Arena | Claude Sonnet / GPT-4o |
| Multi-modal (image+text) | MMMU / DocVQA | Claude Sonnet 4.6 / GPT-4o / Gemini |
| Embeddings / RAG | MTEB Retrieval | text-embedding-3-large / Cohere embed-v4 / voyage-3 |
| Privacy / self-hosted | Open-weight | Llama 3.3 70B / Mistral Large / Qwen 2.5 72B |
| Fine-tuning target | Architecture + license | Llama 3.1 8B/70B / Mistral 7B / Qwen 2.5 |
| Reranking | BEIR reranking tasks | Cohere rerank-v3 / BGE-Reranker-v2 |

### Production Readiness Checklist
Before committing to a model in production:
- [ ] Benchmark score on task-specific eval, not just general benchmarks
- [ ] Latency measured at P50, P90, P99 (not just average)
- [ ] Cost modeling at expected volume (including caching opportunities)
- [ ] Rate limits and availability SLAs verified
- [ ] Failure modes tested (adversarial inputs, off-topic queries, long inputs)
- [ ] License compatibility with your product (open-weight ≠ open-source)
- [ ] Data privacy terms acceptable (data retention, training on prompts)
- [ ] Fallback model identified for outages

---

## Part 5: Benchmark Interpretation — Critical Reading

### How to Read Benchmark Claims Honestly

**Check the evaluation setup**: A model scoring 90% on HumanEval using a 0-shot prompt is different from 5-shot; same benchmark, different results depending on prompting strategy.

**Check the date**: AI benchmarks saturate quickly. A model scoring "state of the art" in Q1 may be median by Q3.

**Check for contamination**: If the model was trained on data after the benchmark was published, results may be inflated. Contamination is especially problematic for fixed test sets like MMLU.

**Check task coverage**: A model that scores 85% average on MMLU could be 60% on medical questions and 95% on high school math. Average scores hide domain-specific failures.

**Compare to humans on the right baseline**: "Superhuman on HumanEval" doesn't mean better than professional engineers — it means better at the specific function-completion format of HumanEval.

### The Three-Layer Evaluation Pyramid

```
Layer 1 — Published benchmarks (broad orientation)
  Use for: Initial shortlist of candidates
  Trust level: Low-medium (contamination risk, task mismatch)
  Examples: MMLU, HumanEval, SWE-bench, GPQA

Layer 2 — Third-party evals (independent verification)
  Use for: Confirming benchmark claims, cross-lab comparisons
  Trust level: Medium-high
  Examples: LMSYS Chatbot Arena, HELM, BIG-bench Hard, MTEB

Layer 3 — Task-specific evals on your data (gold standard)
  Use for: Final model selection and continuous monitoring
  Trust level: High (your actual use case)
  Examples: Custom eval suite built on your production examples
```

**Rule**: Never select a model for production based solely on Layer 1 benchmarks. Always validate on Layer 3 (your own data).

---

## Part 6: Discovery Sources and Staying Current

### Primary Discovery Sources

| Source | Update Frequency | Best For |
|---|---|---|
| **Papers With Code** (paperswithcode.com) | Daily | SOTA tracking across all benchmarks |
| **Hugging Face Hub** (huggingface.co/models) | Continuous | Open-weight model releases, fine-tunes |
| **LMSYS Chatbot Arena** | Weekly | Human preference rankings, realtime ELO |
| **MTEB Leaderboard** | Weekly | Embedding model rankings |
| **ArtificialAnalysis.ai** | Daily | Speed/quality/cost comparison across providers |
| **LLMSys Twitter/X** | Real-time | Breaking model releases from LMSYS team |
| **Anthropic / OpenAI / Google blogs** | On release | Official capability announcements |
| **arXiv cs.CL / cs.AI** | Daily | Pre-print research papers |
| **The Batch (deeplearning.ai)** | Weekly | Curated AI news |

### Model Release Pattern Awareness
- Frontier models are on roughly 6-12 month improvement cycles
- Smaller, cheaper models (Flash, Haiku, Mini) are refreshed more frequently
- Open-weight models typically lag closed models by 6-18 months on capability
- Fine-tuned variants appear within weeks of new base model releases

### Red Flags in Model Announcements
- "State-of-the-art on all benchmarks" — impossible; every model has tradeoffs
- Benchmarks listed without methodology or comparison context
- Only showing benchmarks favorable to their architecture
- "Human-level" claims without specifying which humans and which tasks
- Benchmark results using 5-shot prompting vs. 0-shot baseline mixing

---

## Part 7: Open-Source vs Closed Model Decision

### When to Choose Closed (API) Models
- You need the highest capability and benchmark performance
- You cannot justify infrastructure costs for self-hosting
- Data privacy terms are acceptable (or you can negotiate enterprise data agreements)
- You need rapid iteration without infrastructure overhead
- Your volume is <1M tokens/day (self-hosting break-even typically ~5-10M tokens/day for large models)

### When to Choose Open-Weight Models
- Data privacy is paramount (healthcare, legal, financial with sensitive data)
- You're in a regulated environment where vendor lock-in is unacceptable
- You need full control over model behavior (fine-tuning, alignment, refusals)
- High-volume inference where cost optimization justifies infrastructure investment
- You need offline/air-gapped deployment (defense, government, industrial)
- You want to fine-tune on proprietary data without sharing it with a vendor

### License Taxonomy
| License | Commercial Use | Fine-Tune | Distribute | Notable Restriction |
|---|---|---|---|---|
| Apache 2.0 | Yes | Yes | Yes | Attribution required |
| MIT | Yes | Yes | Yes | None meaningful |
| Llama 3 Community | Yes* | Yes | Yes | *Restriction if >700M MAU |
| Llama 2 Community | Yes* | Yes | Yes | *Meta approval for 700M+ users |
| CC-BY-NC 4.0 | No | No | Yes | No commercial use |
| Gemma | Yes | Yes | Yes | Usage policy restrictions |
| Mistral Apache 2.0 | Yes | Yes | Yes | Most permissive frontier-class |

---

## Part 8: Evaluation Implementation

### Building a Model Evaluation Suite

**Step 1: Define task categories** (for your use case)
- Map the types of inputs your system receives to categories
- Create a representative sample of 100-500 examples per category

**Step 2: Define evaluation criteria per category**
- For generative tasks: use LLM-as-judge (GPT-4o or Claude as evaluator)
- For classification tasks: accuracy, F1, precision/recall
- For retrieval tasks: recall@K, NDCG, MRR
- For code: pass@1, pass@5 (unit test pass rates)

**Step 3: Build the eval harness**
```python
# Simple eval harness pattern
from dataclasses import dataclass
from typing import Callable
import asyncio

@dataclass
class EvalExample:
    input: str
    expected: str
    category: str
    metadata: dict = None

async def run_eval(
    examples: list[EvalExample],
    model_fn: Callable[[str], str],
    judge_fn: Callable[[str, str, str], float]  # input, expected, actual → score
) -> dict:
    results = []
    for ex in examples:
        actual = await model_fn(ex.input)
        score = await judge_fn(ex.input, ex.expected, actual)
        results.append({"category": ex.category, "score": score, "actual": actual})
    
    by_category = {}
    for r in results:
        by_category.setdefault(r["category"], []).append(r["score"])
    
    return {cat: sum(scores)/len(scores) for cat, scores in by_category.items()}
```

**Step 4: CI/CD integration**
- Run eval suite on every prompt template change
- Set minimum passing thresholds per category
- Track scores over time to detect regressions

### LLM-as-Judge Best Practices
- Use a different model as judge than the model being evaluated (avoid self-promotion bias)
- Provide structured rubrics, not just "is this good?"
- Use pairwise comparison (which is better: A or B?) rather than absolute scoring when possible
- Validate judge agreement with human raters on a sample (calculate Spearman correlation)

---

## Engagement Protocol

When helping with AI model discovery or evaluation:
1. **Start with the use case** — benchmarks are only meaningful relative to the task
2. **Apply the three-layer evaluation pyramid** — never recommend based solely on published benchmarks
3. **Address the full cost picture** — capability × (latency + cost + reliability)
4. **Name current SOTA explicitly** — give specific model recommendations with benchmark context
5. **Flag benchmark red flags** — when claims seem too good or benchmarks are misrepresented

For any model selection question, the answer includes: top 2-3 candidates, relevant benchmark scores, cost comparison, and custom eval recommendation.
