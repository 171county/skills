---
name: ai-current-dev
description: Expert-level skill covering the current state of AI model development as of 2025–2026. Covers inference infrastructure, training alignment techniques, fine-tuning methods, RAG pipelines, evaluation frameworks, and the emerging paradigm of context engineering. Use this when reasoning about model selection, training pipelines, serving infrastructure, evaluation strategies, or production AI system design.
---

# AI Current Development Expert

## The Paradigm Shift: From Prompt Engineering to Context Engineering

The dominant framing of 2025: **context engineering** replaces "prompt engineering" as the core skill in applied AI.

> **Context engineering** is the discipline of dynamically constructing the information environment — system prompt, retrieved documents, tool schemas, conversation history, and memory — that maximizes the probability of a desired model output, given cost and latency constraints.

The shift matters because:
- Context windows now exceed 1M tokens (Gemini 2.0 Flash, Claude Opus 4.8 200K native / extended)
- What you put in the context window is more important than model architecture for most production tasks
- "Lost in the middle" degradation (Liu et al., 2023) is partially mitigated by 2026 models, but not eliminated — critical information should still be front-loaded or end-loaded

---

## Frontier Model Landscape (June 2026)

### Closed Proprietary Models

| Model | Provider | Context | Flagship Use Case |
|---|---|---|---|
| **Claude Opus 4.8** | Anthropic | 200K (extended) | Agentic coding, long-document reasoning |
| **GPT-5** | OpenAI | 256K | General reasoning, multimodal |
| **Gemini 2.5 Pro** | Google | 1M | Long-context document processing |
| **Gemini 2.5 Flash** | Google | 1M | Best price/performance in mid-tier |
| **Grok 3** | xAI | 128K | Real-time data (X integration) |

**SWE-bench Pro (production coding benchmark, June 2026):**
- Claude Opus 4.8: **88.6%** (industry-leading agentic coding)
- GPT-5: 84.2%
- Gemini 2.5 Pro: 79.1%

**GPQA Diamond (PhD-level science Q&A):**
- GPT-5: 92.4%
- Claude Opus 4.8: 89.7%
- Gemini 2.5 Pro: 87.3%

### Open-Weight Models

| Model | Parameters | Context | Notes |
|---|---|---|---|
| **Llama 4 Scout** | 17B active (MoE) | 128K | Best 7-17B class open-weight |
| **Llama 4 Maverick** | 400B total (MoE) | 128K | Matches GPT-4o on most benchmarks |
| **DeepSeek-V4** | 671B MoE | 128K | Chinese frontier; controversial data practices |
| **Mistral Large 3** | 123B | 64K | European sovereign, GDPR-native |
| **Qwen 3** | 72B | 32K | Alibaba; strong on multilingual |
| **Phi-4-mini** | 3.8B | 16K | On-device, edge deployment |

**When open-weight wins over proprietary:**
- Data sovereignty / air-gap requirements
- Cost at very high volume (>100M tokens/day)
- Fine-tuning on proprietary data that can't leave your VPC
- Regulatory requirements (EU AI Act, HIPAA, FedRAMP)

---

## Inference Infrastructure

### vLLM (The Production Standard)

**vLLM** is the dominant open-source LLM inference server as of 2025.

**PagedAttention**: Core innovation. KV cache stored in non-contiguous physical memory pages (like OS virtual memory). Eliminates memory fragmentation. Enables:
- 3–4x higher throughput vs. naive KV cache
- Continuous batching (dynamic batch sizing without sequence padding waste)
- Prefix caching (reuse KV cache for shared prompt prefixes)

```bash
# Launch vLLM server
vllm serve meta-llama/Llama-4-Scout-17B-Instruct \
  --tensor-parallel-size 2 \
  --max-model-len 131072 \
  --enable-prefix-caching \
  --enable-chunked-prefill
```

**Key flags:**
- `--enable-prefix-caching`: Critical for system prompts. 40–60% cache hit rate on typical workloads = massive cost reduction
- `--tensor-parallel-size N`: Shard model across N GPUs
- `--enable-chunked-prefill`: Better GPU utilization for mixed prefill/decode batches
- `--guided-decoding-backend outlines`: Structured output (JSON) without quality degradation

**vLLM speculative decoding:**
```python
# Draft model generates 5 tokens; target model verifies in parallel
# Net throughput: 2–3x for latency-sensitive workloads
engine_args = AsyncEngineArgs(
    model="meta-llama/Llama-4-Maverick",
    speculative_model="meta-llama/Llama-4-Scout-17B",
    num_speculative_tokens=5,
)
```

### SGLang (Speed-Optimized Alternative)

**SGLang** (Stanford) is the speed leader for structured generation and multi-call workflows.

**Why it's faster than vLLM for some workloads:**
- **RadixAttention**: Tree-structured KV cache sharing — any shared prefix across requests reuses cache. Better than vLLM's flat prefix caching for multi-turn or few-shot workloads.
- **Compressed finite state machines**: Structured output (JSON, regex) generated without vocabulary masking overhead
- **Jump-forward decoding**: For constrained generation, fills deterministic tokens in one step

**Benchmark (A100 cluster, Llama 4 Maverick, JSON output):**
- SGLang: ~4,800 tokens/s
- vLLM: ~3,200 tokens/s
- TGI: ~2,100 tokens/s

**TGI status**: Hugging Face TGI entered **maintenance mode December 2025**. No new features. Migrate to vLLM or SGLang for new deployments.

### Triton Inference Server (NVIDIA, Enterprise)

For enterprise GPU clusters with NVIDIA hardware:
- Dynamic batching, ensemble models, model repository
- Supports ONNX, TensorRT, PyTorch, TensorFlow backends
- Monitoring via Prometheus/Grafana natively
- Use when you need multi-model serving with heterogeneous backends

### Cloud Inference APIs

```
Anthropic API:         claude-opus-4-8, claude-sonnet-4-6, claude-haiku-4-5-20251001
OpenAI API:            gpt-5, gpt-4.1, gpt-4o-mini
Google AI Studio:      gemini-2.5-pro, gemini-2.5-flash
Together AI:           Llama 4, Mistral — best hosted open-weight pricing
Fireworks AI:          Fast inference, FireAttention optimization
Groq:                  LPU inference — world's fastest latency for supported models
Cloudflare AI:         Edge inference, Workers AI — low-latency global serving
```

---

## Training: Alignment Techniques (Post-Pretraining)

### SFT (Supervised Fine-Tuning) — The Baseline

- Finetune on `(instruction, ideal_response)` pairs
- Still required as the foundation for all RLHF-based methods
- Data quality >> quantity. 1K curated examples > 100K noisy examples.
- Tools: Axolotl, LLaMA-Factory, Unsloth (3x faster SFT with Flash Attention 2)

### DPO (Direct Preference Optimization) — 2024 Mainstream

**Key insight:** Skip the reward model. Directly optimize on `(prompt, chosen_response, rejected_response)` triplets using a reparameterization of the RLHF objective.

```python
from trl import DPOTrainer, DPOConfig

config = DPOConfig(
    beta=0.1,           # KL penalty coefficient
    loss_type="sigmoid",
    learning_rate=5e-7,
)
trainer = DPOTrainer(model, ref_model, config, dataset)
trainer.train()
```

**When DPO fails:** Length bias (model learns to generate longer responses regardless of quality). Use **SimPO** or **IPO** variants.

### SimPO (Simple Preference Optimization) — 2025 Preferred

**Improvement over DPO:** Removes the reference model entirely. Uses sequence-length-normalized reward. Reduces GPU memory 30% vs DPO. Better calibration.

```python
# SimPO: reward = (1/|y|) * log p(y|x) — no reference model needed
config = DPOConfig(
    loss_type="simpo",
    cpo_alpha=0.5,
    simpo_gamma=0.5,
)
```

### GRPO (Group Relative Policy Optimization) — DeepSeek's Contribution

Used in DeepSeek-R1 and now widely adopted for reasoning models.

**Key innovation:** Computes advantages relative to a *group* of outputs for the same prompt, rather than a value function baseline. Enables:
- Better sample efficiency for math/code domains
- No value network required (reduces GPU memory ~40%)
- Naturally produces chain-of-thought reasoning as a byproduct of reward shaping

```python
# GRPO reward shaping for code tasks
def reward_fn(completions: list[str], ground_truth: str) -> list[float]:
    rewards = []
    for c in completions:
        if execute_code(c) == ground_truth:
            rewards.append(1.0)
        elif "def " in c:  # partial credit: at least wrote a function
            rewards.append(0.3)
        else:
            rewards.append(0.0)
    return rewards
```

**GRPO is now the standard for building reasoning models.** If you're building a domain-specific reasoning agent (legal analysis, financial modeling, scientific reasoning), GRPO fine-tuning > DPO for that use case.

---

## Parameter-Efficient Fine-Tuning (PEFT)

Full fine-tuning of 70B+ models requires 500–1000GB VRAM. PEFT methods enable fine-tuning at 1–10% of that cost.

### LoRA (Low-Rank Adaptation) — The Baseline

Decomposes weight updates into two low-rank matrices: `W + AB^T`, where rank r << d.

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(
    r=16,              # rank
    lora_alpha=32,     # scaling factor
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
)
model = get_peft_model(base_model, config)
```

**Parameters:** r=8–64 (lower r = less capacity, faster training; r=64 for complex tasks). Target all attention projections for best results.

### QLoRA — Memory-Efficient Gold Standard

**4-bit NormalFloat quantization** of the base model + LoRA adapters in bf16. Enables fine-tuning a 70B model on a single 80GB A100.

```python
from transformers import BitsAndBytesConfig

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_use_double_quant=True,  # nested quantization saves ~0.4 bits/param
)
```

**QDoRA (2025 emerging standard):** Decompose weight into magnitude + direction components, then apply DoRA (Weight-Decomposed Low-Rank Adaptation) on the directional component. Consistently +1–2% on downstream tasks vs. QLoRA at same memory budget.

### ReFT (Representation Fine-Tuning)

Intervenes on hidden representations rather than weights. Pareto-dominates LoRA on GLUE, instruction following, and reasoning benchmarks at equal parameter count. Still maturing in tooling.

### Practical PEFT Decision Tree

```
Need fine-tuning?
├── Very limited GPU (< 24GB): QLoRA r=8
├── Single 80GB A100: QLoRA r=16–32
├── Multi-GPU (2–4 x 80GB): LoRA r=64 or full fine-tune for 7B
├── Best possible quality, budget available: QDoRA r=32
└── Reasoning/math domain: GRPO first, then LoRA on top
```

---

## RAG (Retrieval-Augmented Generation) Pipelines

### Architecture Overview

```
[Documents] → [Chunking] → [Embedding] → [Vector Store]
                                              ↓
[User Query] → [Retrieval] → [Reranking] → [LLM] → [Response]
```

### Chunking Strategy

**Fixed-size**: Split every 512 tokens. Fast but breaks semantic units.
**Semantic**: Split at paragraph/section boundaries. Requires more preprocessing.
**Recursive character splitter (default)**: Split by `\n\n`, `\n`, ` ` in order. Good balance.
**Late chunking**: Embed full document first, then chunk embeddings (not tokens). Best quality for long documents. Use with `jina-embeddings-v3`.

**The overlap parameter**: 10–20% overlap between chunks prevents important information from falling between chunk boundaries.

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=512,
    chunk_overlap=64,
    separators=["\n\n", "\n", ". ", " ", ""],
)
```

### Hybrid Search (Dense + Sparse)

Pure vector search misses exact-match queries. Pure BM25 misses semantic queries. Hybrid wins on both:

```python
# Reciprocal Rank Fusion — combines rankings from multiple retrievers
from langchain.retrievers import EnsembleRetriever

retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, vector_retriever],
    weights=[0.5, 0.5],
)
# RRF score: sum(1 / (k + rank_i)) for each system's ranking
```

**Vector stores (2025 production choices):**
- **pgvector**: If you're already on PostgreSQL. Best for < 10M vectors.
- **Pinecone Serverless**: Zero-ops, pay-per-query. Best for startups.
- **Weaviate**: Good balance of hybrid search + filtering.
- **Qdrant**: Highest raw query performance for large collections. Rust-based.

### Reranking — The Most Underused Quality Boost

A reranker reads the full `(query, chunk)` pair and assigns a relevance score. Retrieval (embedding-based) is recall-optimized; reranking is precision-optimized.

```python
from cohere import Client

co = Client(api_key)
results = co.rerank(
    model="rerank-english-v3.5",
    query=user_query,
    documents=[chunk.text for chunk in retrieved_chunks],
    top_n=5,
)
# Use results.results[i].relevance_score to filter
```

**Benchmarks:** Adding Cohere Rerank 3.5 to any retrieval pipeline improves nDCG@10 by 8–15% on average.

**Open-source reranker:** `BAAI/bge-reranker-v2-m3` — competitive with Cohere at zero API cost; requires hosting.

### Advanced RAG Patterns

**HyDE (Hypothetical Document Embedding):** Generate a hypothetical ideal answer, embed it, use it for retrieval. Better for abstract queries.

**FLARE (Forward-Looking Active Retrieval):** Retrieve mid-generation when the model is uncertain (low token probability). Dynamic retrieval.

**Corrective RAG (CRAG):** After retrieval, have a critic LLM assess document relevance. If low, trigger web search. If high, proceed. If ambiguous, refine query.

**Graph RAG:** Microsoft's approach. Build a knowledge graph from documents. Retrieve via graph traversal + vector similarity. Better for "global" questions that require synthesizing many documents.

---

## Evaluation Frameworks

### RAGAS — RAG Pipeline Evaluation

```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,          # Is the answer grounded in retrieved context?
    answer_relevancy,      # Does the answer address the question?
    context_precision,     # Are retrieved chunks actually relevant?
    context_recall,        # Were all relevant chunks retrieved?
)

result = evaluate(
    dataset=eval_dataset,  # {question, answer, contexts, ground_truth}
    metrics=[faithfulness, answer_relevancy, context_precision, context_recall],
)
# Faithfulness < 0.7: your model is hallucinating over retrieved context
# Context recall < 0.7: your retrieval is missing relevant documents
```

**Production target scores:** All four metrics > 0.80 before shipping.

### DeepEval — LLM-as-Judge Evaluation

```python
from deepeval.metrics import GEval, HallucinationMetric

metric = GEval(
    name="Correctness",
    criteria="Is the output factually correct given the context?",
    evaluation_params=[LLMTestCaseParams.INPUT, LLMTestCaseParams.ACTUAL_OUTPUT],
    model="claude-opus-4-8",
)
```

**promptfoo** — regression testing for prompts:

```yaml
# promptfooconfig.yaml
prompts:
  - "Summarize this contract: {{contract_text}}"
providers:
  - openai:gpt-5
  - anthropic:claude-opus-4-8
tests:
  - vars:
      contract_text: "{{file://contracts/test1.txt}}"
    assert:
      - type: contains
        value: "payment terms"
      - type: llm-rubric
        value: "The summary must identify all parties and key dates"
```

### Benchmark Trust Hierarchy

When evaluating models for your use case:

```
Trust highest:    HLE (Humanity's Last Exam — PhD-level, held-out)
                  GPQA Diamond (Google-Proof Q&A, expert-verified)
                  AIME 2025 (math olympiad, objective)
Trust moderate:   SWE-bench Pro (coding, real issues, harder split)
                  LiveCodeBench (rolling, fresh problems)
Trust carefully:  SWE-bench Verified (original, now saturated)
                  LMSYS Chatbot Arena (human preference, has biases)
Trust least:      MMLU (saturated since 2024, data contamination widespread)
                  HellaSwag (trivially solved by frontier models)
```

---

## LLM Observability and Monitoring in Production

### What to Trace

Every LLM call in production should emit:
```json
{
  "trace_id": "uuid",
  "model": "claude-sonnet-4-6",
  "input_tokens": 1240,
  "output_tokens": 380,
  "latency_ms": 1840,
  "cached_tokens": 880,
  "cost_usd": 0.00412,
  "finish_reason": "stop",
  "tags": ["rag_query", "user_id:u123"]
}
```

### Prompt Caching Economics

Anthropic Claude prompt caching (launched 2024, now standard):
- Cache write: 1.25x base input price
- Cache read: 0.1x base input price (90% discount)
- Cache TTL: 5 minutes (ephemeral) or 1 hour (extended)

**For RAG systems:** Cache your system prompt + retrieval instructions. Only the retrieved context changes per query. Typical savings: 60–80% of input token cost.

### Drift Detection

Models change (API updates, silent retraining). Monitor:
- Response length distribution (sudden shift = model changed)
- Refusal rate (increasing = safety threshold changed)
- Embedding cosine similarity distribution (embedding model updated = RAG retrieval breaks)

Set alerts: if refusal rate doubles over a 7-day rolling window, page on-call.

---

## The AI Stack in 2026: Layers and Best-in-Class

```
Layer               Tool                    Notes
─────────────────────────────────────────────────────────────
Frontier model      Claude Opus 4.8         Coding/reasoning
                    Gemini 2.5 Flash        Cost-optimized
                    Llama 4 Maverick        Open-weight option
Inference server    vLLM / SGLang           PagedAttention / RadixAttention
Embedding           Voyage-3                Best retrieval quality
                    text-embedding-3-large  OpenAI compatibility
Reranking           Cohere Rerank 3.5       Hosted
                    bge-reranker-v2-m3      Self-hosted
Vector DB           pgvector                If already on PG
                    Pinecone Serverless     Zero-ops
                    Qdrant                  High-performance
Orchestration       LangGraph               Production multi-agent
                    OpenAI Agents SDK       OpenAI-native
Fine-tuning         QDoRA / GRPO            PEFT + alignment
Evaluation          RAGAS + DeepEval        RAG + LLM-judge
Observability       LangSmith / Langfuse    Tracing + eval
Prompt management   Braintrust / Promptfoo  A/B testing + CI
```

---

## Context Window Management: Practical Patterns

### KV Cache Optimization

```python
# Anthropic: cache your static context
messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "text",
                "text": static_system_context,
                "cache_control": {"type": "ephemeral"}  # cache this block
            },
            {
                "type": "text",
                "text": dynamic_user_query  # not cached; changes per request
            }
        ]
    }
]
```

### Long Context Best Practices

- **Front-load critical information**: "Lost in the middle" is real even for 2026 models. Put the most important facts first or last.
- **Explicit section markers**: `<context>`, `<instructions>`, `<examples>` tags improve parsing on long contexts.
- **Needle-in-haystack testing**: Before deploying a long-context application, run NIAH tests at 50%, 75%, and 100% of your max context length.
- **Token budget control**: Set `max_tokens` per call. Never let an inference call run open-ended in production.

---

## Emerging Techniques (2025–2026)

### Test-Time Compute Scaling

**Key insight from o1/o3/R1 era:** Scaling inference compute (more thinking tokens) often yields better results than scaling model size. A 70B model with 10K thinking tokens can beat a 405B model with 1K tokens on hard reasoning tasks.

**Implications:**
- Build latency budgets that allow for "thinking models"
- For agentic tasks where latency is acceptable, prefer `extended_thinking` mode
- For interactive chat, use standard mode

### Mixture of Experts (MoE) Efficiency

Llama 4 Scout (17B active, 109B total), DeepSeek-V4 (37B active, 671B total) — run inference at the cost of the *active* parameters, not total.

**For self-hosted deployments:** MoE models give you frontier-class quality at 70B inference cost. The catch: all expert weights must fit in VRAM even if only a subset activate.

### Multimodal Natively

2026 standard: all frontier models are multimodal (text + image + audio + video + code). Design your pipelines to be modality-agnostic from day one. `content` arrays should accept `image_url` and `document` types, not just `text`.
