---
name: ai-current-dev-expert
description: Expert-level AI development covering frontier model selection (Claude Opus 4.8, GPT-5, Gemini 2.5 Pro, Llama 4, DeepSeek), prompting techniques, fine-tuning (LoRA/QLoRA/DPO), RAG architectures, vector databases, inference optimization, LLM APIs, observability, and production deployment. Use when building AI applications, choosing models, optimizing LLM pipelines, debugging AI systems, or making architecture decisions for AI-powered products.
---

# AI Current Dev Expert

## Frontier Model Landscape (June 2026)

### Closed-Source Leaders

**Claude Opus 4.8 (Anthropic)**
- Ranked #1 on Artificial Analysis composite benchmark (score: 61)
- Pricing: $5/M input tokens, $25/M output tokens
- Context: 200K tokens; native computer use API; extended thinking mode
- Strengths: coding (SWE-bench leader), complex reasoning, tool use, safety
- Extended thinking: exposes chain-of-thought, interleaved with tool calls
- Claude Sonnet 4.6: $3/M input, $15/M output — best cost/performance for most production workloads
- Claude Haiku 4.5: fastest/cheapest for high-volume, latency-sensitive tasks

**GPT-5 (OpenAI)**
- 94.6% on AIME 2025 (math olympiad problems)
- 400K context window
- Multimodal: text, image, audio, video input
- Native function calling with parallel tool execution
- **GPT-5.2**: >90% ARC-AGI-1 (extreme reasoning benchmark); not yet public pricing (research preview)

**Gemini 2.5 Pro (Google DeepMind)**
- 1M context (expanding to 2M); handles 750-page PDFs, entire codebases
- Thinking budgets: configurable compute allocation (thinkingBudget: 0-32768 tokens)
- Native multimodal trained end-to-end (not adapter-based)
- Strong: long-context retrieval, code generation, scientific reasoning
- Pricing: $1.25/M (≤200K input), $2.50/M (>200K input), $10/M output

### Open-Source Leaders

**Meta Llama 4 Maverick**
- Architecture: 17B active parameters / 400B total (128-expert Mixture of Experts)
- 128K context window; 8-expert activation per token
- Multimodal: image + text
- Performance: matches GPT-4o on most benchmarks
- License: Llama 4 Community License (commercial use permitted with conditions)

**Meta Llama 4 Scout**
- 17B active / 109B total (16-expert MoE)
- **10M context window** — largest in open-source
- Efficient for document analysis, long-form reasoning
- Runs on single 8xH100 node

**DeepSeek R1-0528**
- MIT License (fully open, including weights)
- Approaches o3 performance on math and coding benchmarks
- Chain-of-thought reasoning with <think> blocks exposed
- Available via DeepSeek API, Together AI, Groq, Fireworks
- **Critical advantage**: can be fine-tuned, self-hosted, and modified commercially

**Qwen 3 (Alibaba)**
- Flagship: 235B MoE model; smaller versions: 0.6B to 32B dense
- **Toggleable thinking mode**: /think and /no_think tokens switch reasoning on/off
- Strong: multilingual (29 languages), coding, math
- Qwen 3-30B-A3B: 30B total, 3B active MoE — exceptional efficiency

### Model Selection Decision Framework
```
Task Type → Model Choice:
├── Complex reasoning + coding → Claude Opus 4.8 or GPT-5
├── Production API calls (quality) → Claude Sonnet 4.6
├── High-volume, latency-sensitive → Claude Haiku 4.5 or Llama 4 Scout
├── Long-document analysis → Gemini 2.5 Pro (1M+ context)
├── Self-hosted / fine-tuning → Llama 4 Maverick or DeepSeek R1-0528
├── Math/science reasoning → DeepSeek R1-0528 or GPT-5
└── Multilingual production → Qwen 3 32B or Claude Sonnet 4.6
```

---

## Prompting Techniques

### Foundation Techniques

**Zero-Shot Prompting**
- Direct task instruction without examples
- Best for: clear, well-defined tasks; capable models
- Limitation: fails on novel formats, domain-specific schemas

**Few-Shot Prompting**
- 3-8 input/output examples in prompt
- Selection criteria: diverse examples, edge cases represented, consistent format
- Shot count: 3-5 often optimal; more examples ≠ always better
- Dynamic few-shot: retrieve relevant examples from example store via semantic search

**Chain-of-Thought (CoT)**
- "Let's think step by step" or explicit step decomposition
- Improves complex reasoning, math, multi-step problems
- **Self-Consistency CoT**: sample N completions with temperature > 0, majority vote
  - +17.9% on GSM8K (math benchmark) vs. greedy decoding
  - Cost: N× token usage; use when accuracy > cost

**Structured Output Prompting**
- Specify exact JSON schema in system prompt
- Use XML tags for structured sections: `<thinking>`, `<answer>`, `<citations>`
- Pydantic model in prompt → LLM mirrors the schema

### Advanced Techniques

**ReAct (Reasoning + Acting)**
- Alternates Thought → Action → Observation
- Enables tool use within reasoning loop
- Foundation for most agentic systems

**Tree of Thoughts (ToT)**
- Branch multiple reasoning paths, evaluate each, prune weak branches
- 74% on Game of 24 vs 4% CoT (original paper)
- High cost; use when solution space exploration is critical

**Prompt Chaining**
- Decompose complex tasks into sequential prompts
- Output of each step → input of next
- Reduces hallucination by constraining each step's scope

**Contextual Retrieval (Anthropic, 2024)**
- Prepend chunk-specific context before embedding: "This passage from [document] discusses X in the context of Y"
- Reduces retrieval failures by 49% in testing
- Combine with BM25 for hybrid retrieval

### System Prompt Architecture
```
System: [Role + Expertise] + [Task Context] + [Output Format] + [Constraints]

Example structure:
"You are an expert [domain] assistant. Your task is [specific task].
You have access to [tools/context].
Always respond in [format: JSON/markdown/structured].
Constraints: [list specific rules].
If uncertain about [X], [behavior specification]."
```

---

## Fine-Tuning

### When to Fine-Tune vs. Prompt Engineer
| Scenario | Approach |
|----------|----------|
| Task performance gap after extensive prompting | Fine-tune |
| Need specific output format consistently | Fine-tune |
| Domain-specific knowledge (dense proprietary) | RAG (not fine-tune) |
| Reduce prompt token usage | Fine-tune (bake context into weights) |
| Behavior/personality alignment | Fine-tune (RLHF/DPO) |
| General capability improvement | Full pre-training (not viable for most) |

### LoRA (Low-Rank Adaptation)
- Freeze base model weights; inject trainable rank-decomposition matrices
- Rank parameter `r`: 8-64 (higher r = more capacity but more memory)
  - r=8: minimal parameter count, subtle adaptation
  - r=32: balanced for most fine-tuning
  - r=64: complex task adaptation, larger data requirements
- Alpha parameter: controls scaling (alpha/r = effective learning rate scaling)
- Target modules: q_proj, v_proj (attention); extend to k_proj, o_proj, mlp for stronger adaptation
- **Parameter count**: LoRA r=8 on 7B model ≈ 5M trainable params vs 7B base params

### QLoRA (Quantized LoRA)
- 4-bit NF4 (NormalFloat4) quantization of base model
- Double quantization: quantize the quantization constants
- Reduces memory by ~75% vs full precision
- 7B model: ~5GB VRAM (vs 14GB FP16, 28GB FP32)
- Training cost: fine-tune 70B model on single A100
- Minimal quality loss vs full LoRA for most tasks

**Practical QLoRA Setup (HuggingFace PEFT)**
```python
from transformers import BitsAndBytesConfig
from peft import LoraConfig, get_peft_model

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16
)

lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)
```

### Alignment Techniques

**RLHF (Reinforcement Learning from Human Feedback)**
- Three stages: SFT → reward model training → PPO optimization
- Complex, requires significant infrastructure
- Now largely replaced by DPO for most use cases

**DPO (Direct Preference Optimization)**
- Directly optimizes on (chosen, rejected) preference pairs
- No reward model required; single-stage training
- Equivalent performance to RLHF in most settings
- **Default alignment method for open-source models (2024-2026)**
- Dataset format: {prompt, chosen_response, rejected_response}

**ORPO (Odds Ratio Preference Optimization)**
- Single-pass SFT + alignment (no separate alignment stage)
- 10-15% faster training than DPO pipeline
- Slightly lower peak performance than DPO in ablations but more efficient

### Model Merging
- Combine multiple fine-tuned models without retraining
- **SLERP**: spherical linear interpolation between two models' weights
- **TIES-Merging**: resolves parameter conflicts via trimming and sign agreement
- **DARE**: random parameter dropping + rescaling before merge
- **Evolutionary merge**: use search algorithms to find optimal merge coefficients
- Tools: `mergekit` (arcee-ai), works with any safetensors model
- Use case: merge a coding-specialized model with an instruction-following model

---

## RAG Architectures

### Architecture Evolution

**Naive RAG (Baseline)**
- Index documents → embed query → retrieve top-k → stuff context → generate
- Limitations: irrelevant chunks, context overflow, no query expansion
- Chunk size impact: 512 tokens ±50% optimal for most use cases; smaller = precise retrieval, larger = more context

**Advanced RAG Techniques**

**HyDE (Hypothetical Document Embeddings)**
- Generate hypothetical answer → embed hypothetical → retrieve with hypothetical embedding
- Bridges query-document embedding gap
- 15-25% retrieval precision improvement on most benchmarks

**Multi-Query Retrieval**
- Generate N query variants from original query
- Retrieve for each variant, deduplicate results
- Captures different aspects of information need

**Parent-Child Chunking**
- Embed small child chunks (128-256 tokens) for precision
- Retrieve parent chunks (512-1024 tokens) for context richness
- Decouples retrieval precision from context completeness

**Reranking (Cross-Encoder)**
- First-stage: fast bi-encoder retrieval (top-50)
- Second-stage: cross-encoder reranker (top-5 from top-50)
- Models: Cohere Rerank v3, BGE-Reranker-v2, Jina Reranker
- Adds 50-200ms latency but significantly improves precision

**Contextual Retrieval (Anthropic)**
- Prepend context to each chunk before embedding: "In [doc], this chunk discusses [X]..."
- Claude Haiku generates context; relatively cheap at scale
- 49% reduction in retrieval failures in Anthropic benchmarks

**Self-RAG**
- LLM controls retrieval: decide whether to retrieve, what to retrieve, how to use it
- CRITIC tokens: [Retrieve], [ISREL], [ISSUP], [ISUSE]
- Complex to implement; use when retrieval quality is highly variable

**Agentic RAG**
- Multi-step retrieval with planning
- Agent can issue multiple queries, use different retrieval strategies per query
- Sub-queries for complex questions, iterative refinement
- Use: complex research tasks, multi-hop reasoning

**Graph RAG (Microsoft, 2024)**
- Build knowledge graph from documents
- Community detection → hierarchical summaries
- Query against graph + summaries, not just raw chunks
- 30-45% improvement on global synthesis questions
- High indexing cost: not suitable for rapidly changing document stores
- Best for: static corpora, complex analytical questions

### Vector Database Selection

| Database | Best For | Throughput | Notes |
|----------|----------|------------|-------|
| Pinecone | Managed, production | High | $70/M vectors/month (p1); zero ops |
| Qdrant | Self-hosted, fastest | Highest raw | Rust-native; on-disk indexing; 4x cost advantage vs Pinecone |
| Weaviate | Multi-modal, hybrid | High | GraphQL API; built-in BM25+vector hybrid |
| Milvus | Enterprise scale | Very high | Billion-scale; distributed; complex ops |
| Chroma | Development, local | Moderate | Python-native; no production persistence built-in |
| pgvector | Existing Postgres | Moderate | 400K vectors accurately; HNSW index in Postgres 15+ |
| pgvectorscale | Postgres at scale | High | TimescaleDB extension; StreamingDiskANN; 28x faster than pgvector |

**Indexing Algorithms**
- **HNSW**: best recall/speed tradeoff; O(log n) queries; high memory
- **IVF (Inverted File)**: lower memory; slightly worse recall; good for billion-scale
- **DiskANN**: disk-based ANN; near-HNSW performance with fraction of RAM

**Hybrid Search**
- Combine dense vector (semantic) + sparse BM25/TF-IDF (keyword)
- RRF (Reciprocal Rank Fusion): score = Σ 1/(k + rank_i), k=60
- Best for: queries with specific terminology, product names, codes

---

## Inference Optimization

### Inference Servers

**vLLM**
- PagedAttention: virtual memory management for KV cache
- 793 tokens/second on A100 (LLaMA-3-8B)
- Continuous batching: dynamic request batching
- OpenAI-compatible API; supports most HuggingFace models
- Best for: general production serving

**SGLang**
- RadixAttention: KV cache reuse across shared prefixes
- 29% better throughput than vLLM on shared-prefix workloads (chat, RAG with system prompt)
- CUDA graph optimization; structured generation (JSON, regex) at inference speed
- Best for: high-traffic production, RAG systems, structured output

**TensorRT-LLM (NVIDIA)**
- NVIDIA-native; maximizes H100/A100 utilization
- INT8/FP8 quantization at inference time
- ~2-3x throughput improvement over vLLM in NVIDIA benchmarks
- Best for: NVIDIA-only infrastructure, latency-critical applications

**Ollama**
- 41 tokens/second on M3 Max (LLaMA-3-8B)
- Zero-config local serving
- REST API, auto-download, GPU acceleration (Apple Silicon, NVIDIA, AMD)
- Best for: development, testing, privacy-sensitive local deployment

**llama.cpp**
- Maximum quantization support (Q2, Q3, Q4, Q5, Q6, Q8)
- CPU-capable (AVX2, Apple Silicon optimized)
- Lowest memory footprint
- Best for: edge deployment, CPU-only inference, consumer hardware

### Quantization Impact
| Format | Size (7B) | Quality Loss | Use Case |
|--------|-----------|-------------|----------|
| FP32 | 28GB | None (baseline) | Training only |
| BF16 | 14GB | Negligible | Inference production |
| FP8 | 7GB | ~0.1% | GPU production, TensorRT |
| Q8_0 | 7GB | ~0.1% | Good CPU option |
| Q4_K_M | 4.1GB | ~1% | Balanced local inference |
| Q3_K_M | 3.1GB | ~2-3% | Memory-constrained |
| Q2_K | 2.5GB | ~5%+ | Emergency/minimal hardware |

---

## LLM APIs and Providers

### API Selection Matrix

**Anthropic API**
- Models: Claude Opus 4.8, Sonnet 4.6, Haiku 4.5
- Unique: computer use API, extended thinking, prompt caching (90% cost reduction on static context)
- Tool use: native, parallel tool calling
- SDK: Python + TypeScript

**OpenAI API**
- Models: GPT-5, GPT-4o, o1-pro, GPT-4o-mini
- Unique: Assistants API, function calling, structured outputs (Pydantic-native)
- Realtime API: WebSocket-based, streaming audio/text
- Batch API: 50% cost reduction, 24-hour turnaround

**Google AI Studio / Vertex AI**
- Models: Gemini 2.5 Pro, Flash, Flash-8B
- Unique: 1M+ context, code execution tool, grounding (Google Search)
- Vertex: enterprise-grade, VPC Service Controls, model evaluation

**Groq**
- Specialized: LPU (Language Processing Unit) inference hardware
- TTFT: <300ms consistently (fastest available)
- Models: Llama 4, Gemma 3, Mixtral, Whisper
- Best for: latency-critical applications, real-time conversational AI

**Fireworks AI**
- Ultra-low latency; function calling with <100ms TTFT
- FireFunction-v2: specialized function calling model
- Pricing: $0.20/M tokens (Llama 4 Maverick)

**Together AI**
- 200+ open models; fine-tuning pipeline included
- Custom model deployment
- Competitive: $0.18/M tokens (Llama 4 Scout)

---

## Observability and Evaluation

### Tracing and Monitoring

**LangSmith (LangChain)**
- Traces every LLM call, tool use, chain execution
- Dataset creation from production traces
- A/B testing prompts against datasets
- Integrated with LangChain/LangGraph natively; Python SDK for any LLM framework

**Braintrust**
- Eval-first platform; scores generations with LLM-as-judge
- Experiment tracking (prompt versions → score comparisons)
- BTAI: online eval scoring production traffic
- Best for: systematic prompt engineering, regression testing

**Arize Phoenix / Arize AI**
- Open-source (Phoenix) + managed (Arize)
- OTEL-compatible tracing (OpenTelemetry)
- RAG evaluation: hallucination detection, context relevance, answer faithfulness
- Drift detection for production LLMs

### Evaluation Frameworks

**RAGAS (RAG Assessment)**
- Metrics: Faithfulness, Answer Relevance, Context Precision, Context Recall
- Faithfulness: claims in answer supported by context (0-1)
- LLM-as-judge + semantic similarity scoring
- Reference-free evaluation (no ground truth needed for some metrics)

**DeepEval**
- 14+ metrics: G-Eval, hallucination, bias, toxicity, summarization
- Pytest integration: assert metric_score > threshold
- Red-teaming module

**TruLens**
- RAG triad: Answer Relevance + Context Relevance + Groundedness
- Feedback functions using OpenAI/Anthropic as judge
- Streamlit dashboard

**LLM-as-Judge Pattern**
```python
# Standard evaluation prompt
judge_prompt = """
Rate the following response on a scale of 1-5:
1=Poor, 2=Fair, 3=Good, 4=Very Good, 5=Excellent

Response: {response}
Question: {question}
Reference: {reference}

Provide score and reasoning in JSON: {"score": X, "reasoning": "..."}
"""
```

---

## Structured Output

### Instructor Library (Python)
- Wraps any LLM API to return Pydantic models
- Automatic retry on validation failure
- Supports Anthropic, OpenAI, Gemini, Mistral, Groq

```python
import instructor
from anthropic import Anthropic
from pydantic import BaseModel

client = instructor.from_anthropic(Anthropic())

class UserInfo(BaseModel):
    name: str
    age: int
    email: str

user = client.chat.completions.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Extract: John Doe, 30, john@example.com"}],
    response_model=UserInfo,
)
```

### Native JSON Schema (OpenAI + Anthropic)
- OpenAI: `response_format={"type": "json_schema", "json_schema": {...}}`
- Anthropic: tool use with required tool call forces structured output
- Pydantic AI: framework for structured LLM outputs with validation

---

## Production Architecture Patterns

### Prompt Caching (Anthropic)
- Cache static context (system prompts, documents, examples) → 90% cost reduction
- Cache TTL: 5 minutes standard, ephemeral cache
- Use: RAG system prompts, large static documents, few-shot examples
- Implementation: `cache_control: {"type": "ephemeral"}` on message blocks

### Model Routing
- Route simple queries to cheap models, complex to expensive
- Routers: MartianAI, RouteLLM (open-source classifier-based)
- 60-80% total cost savings with quality parity
- Strategy: if complexity_score < threshold → Haiku; elif medium → Sonnet; else → Opus

### Streaming
- Stream tokens as generated; reduce perceived latency
- `stream=True` in all major APIs
- SSE (Server-Sent Events) for web applications
- Token-by-token vs. chunk-by-chunk streaming

### Rate Limiting and Retry Strategy
```python
import anthropic
from anthropic import RateLimitError, APIStatusError
import time

def call_with_retry(client, max_retries=5):
    for attempt in range(max_retries):
        try:
            return client.messages.create(...)
        except RateLimitError:
            wait = (2 ** attempt) + random.uniform(0, 1)  # exponential backoff + jitter
            time.sleep(wait)
        except APIStatusError as e:
            if e.status_code >= 500:  # transient server errors
                time.sleep(2 ** attempt)
            else:
                raise  # 4xx = don't retry
    raise Exception("Max retries exceeded")
```

---

## Expert Calibration Notes

- **Model choice is not permanent**: benchmark for your specific task using your actual prompts and data — general benchmarks often don't translate
- **Prompt engineering yields 80% of the gains before fine-tuning**: exhaust prompting, few-shot, CoT before incurring fine-tuning complexity
- **RAG quality is primarily a chunking and retrieval problem**: most RAG failures trace to poor chunking strategy or insufficient reranking, not the LLM
- **Streaming matters for UX, not performance**: total time is the same; streaming just moves the perception
- **TTFT (time to first token) ≠ throughput**: Groq wins TTFT, vLLM wins total throughput — choose based on your user-facing requirements
- **LLM-as-judge creates circular evaluation**: use a different model family for evaluation than generation when possible; cross-model evaluation is more reliable
- **Open-source models are production-ready for most tasks**: Llama 4 Maverick and DeepSeek R1-0528 at 2026 capability levels handle 85%+ of production AI use cases
