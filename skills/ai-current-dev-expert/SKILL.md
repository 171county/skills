---
name: ai-current-dev-expert
description: Expert-level advisor on current AI/ML development practices. Use this skill whenever the user asks about LLM training (SFT, RLHF, DPO, GRPO), fine-tuning (LoRA, QLoRA), prompt engineering (chain-of-thought, structured outputs, tool use), RAG (retrieval-augmented generation, vector stores, chunking, hybrid search, reranking), inference optimization (vLLM, quantization, speculative decoding), LLM evaluation (RAGAS, LLM-as-judge, CI/CD for AI), AI application architecture (async, streaming, microservices), model deployment (cloud APIs, self-hosted, Ollama, vLLM, LiteLLM), multimodal development, MLOps/LLMOps, AI security (prompt injection, guardrails), or reasoning models (o-series, extended thinking, test-time compute).
---

# AI Current Development Expert

You are an expert-level AI/ML development practitioner. Give precise, production-grade technical guidance. All information current as of June 2026.

---

## 1. LLM Training and Post-Training (2025-2026)

The revolution has been entirely in **post-training**. Pre-training is stable (trillion-token datasets, transformer with 1M+ context, MoE scaling). The 2026 canonical post-training stack:

### Alignment Techniques

**DPO (Direct Preference Optimization)**: Pairwise alignment without a separate reward model. Simpler than PPO+RLHF. Variant **SimPO** beats classic DPO by 6.4 points on AlpacaEval 2. **KTO** works with binary (thumbs up/down) feedback — easier to collect in production.

**GRPO (Group Relative Policy Optimization)**: The technique behind DeepSeek-R1. Instead of a critic model, GRPO compares a batch of model outputs against each other, assigns relative rewards, and optimizes via verifiable correctness signals (math answers, code execution). This enabled DeepSeek-R1 to match OpenAI o1 at a fraction of training cost.

**DAPO (Decoupled Clip and Dynamic Sampling Policy Optimization)**: Follow-on to GRPO. Achieves +3 points on AIME 2024 with 50% fewer training steps.

**RLVR (Reinforcement Learning with Verifiable Rewards)**: The dominant 2026 paradigm for reasoning-capable models. Reward signals come from programmatic verification (unit tests, math checkers, formal provers) rather than human annotation. Enables cost-effective training of frontier-class reasoning.

**Note on terminology**: When a 2026 paper references "RLHF," it typically means a DPO + GRPO hybrid stack, not OpenAI's original PPO formulation.

---

## 2. Prompt Engineering Mastery

Prompt engineering is now a production engineering discipline with version control, caching strategy, and model-specific syntax.

### Chain-of-Thought (CoT)
Still highly effective for standard models. Canonical pattern:
```
<reasoning>Think step by step about X</reasoning>
<answer>Return JSON only</answer>
```
**Critical caveat**: Skip explicit CoT for reasoning models (o-series, Claude Extended Thinking, Gemini Thinking Mode) — they already perform internal deliberation. Adding CoT wastes tokens and can degrade performance.

### Few-Shot
3-5 diverse, well-chosen examples wrapped in model-specific tags (XML for Claude, markdown for GPT) consistently outperform additional instruction text. Quality beats quantity.

### System Prompts
Front-load role and task in the first 200 tokens — attention patterns weight early content heavily. System prompts have grown from ~50 tokens (2023) to 500-5,000 tokens (2026).

### Structured Outputs
Claude Opus 4.x responds best to XML-tagged instructions; GPT-5.x prefers concise JSON schemas. Use native structured output modes (function calling, constrained decoding) over post-processing.

### Key Production Discipline
Treat prompts as code: version-control them, run regression tests on changes, cache static content (system instructions, few-shot examples, tool definitions), place variable content last to maximize KV cache hit rates.

---

## 3. RAG: Production Patterns

Industry data: when RAG fails in production, **retrieval is the failure point 73% of the time**, not generation. Naive RAG (chunk → embed → cosine similarity → stuff prompt) is a prototype pattern.

### Chunking Strategies (2026 Best Practices)
- Split on meaning (document sections, headings, semantic boundaries), not arbitrary character counts
- Different document types need different sizes: API docs → small precise chunks; narrative text → larger contextual chunks
- **Hierarchical chunking**: Store small chunks for retrieval, link to parent chunks for context injection

### Retrieval Stack (Minimum Viable Production)
**Hybrid Search = BM25 (sparse/keyword) + dense vector + cross-encoder reranker**

This is now the minimum viable production pattern. Pure dense retrieval is insufficient.

**Cross-encoder reranking**: Cohere Rerank, BGE-Reranker-v2, Jina Reranker. Rescores retrieved chunks by full query-document attention. Described as the highest single ROI upgrade in RAG systems.

### Vector Stores

| DB | Best For |
|---|---|
| **Pinecone** | Production-ready, zero-ops, SaaS |
| **Weaviate** | LangChain-native, flexible, open-source |
| **Qdrant** | High-performance, Rust-based, strong filtering |
| **Chroma** | Developer prototyping, fast start |
| **pgvector** | Existing Postgres users |
| **Milvus** | Billion-scale vector search |

### Advanced RAG Patterns (2026)
- **GraphRAG**: Entity and relationship extraction for knowledge-graph-aware retrieval (interconnected enterprise data)
- **HyDE (Hypothetical Document Embedding)**: Generate hypothetical answer, embed it, then retrieve — bridges query-document distribution gap
- **Contextual compression**: Post-retrieval filtering to retain only relevant portions of retrieved chunks before injection
- **Agentic RAG**: Agents dynamically decide when to retrieve, what to query, how to synthesize across multiple retrieval passes

---

## 4. Fine-Tuning in Practice

**Decision sequence (follow strictly)**: Prompt first → RAG second → Fine-tune third → Distill fourth. ~80% of "we need fine-tuning" requests resolve with better retrieval or prompting.

**Fine-tune when**:
- Task is narrow and well-defined
- Format/style consistency is critical
- Latency or cost constraints make large-model prompting unworkable
- Need to inject stable domain knowledge not suited to context

### LoRA / QLoRA (Default for Nearly Every Team)

**LoRA**: Low-Rank Adaptation adds trainable rank-decomposition matrices to frozen base model weights. Rank 16-64 covers most use cases.

**QLoRA**: 4-bit quantized base model + LoRA adapters. Practical benchmark: fine-tuning Llama 3 8B on 50K examples costs ~$12 on a single A100 80GB (6 hours). **This is the 2026 default.**

Full fine-tuning only when compute budget is unconstrained and maximum performance is required.

**Data quality principle**: 1,000 hand-curated, high-quality examples consistently outperform 100,000 noisy ones. Data curation is the highest-leverage investment in fine-tuning.

**Evaluation protocol**: Baseline on MMLU, GSM8K, HumanEval + domain-specific eval set before and after. Monitor for catastrophic forgetting.

---

## 5. Inference Optimization

### Quantization
- **GGUF (llama.cpp)**: CPU-optimized, standard for local/edge deployment
- **AWQ (Activation-Aware Weight Quantization)**: Better quality than GPTQ at equivalent bit widths; preferred format for GPU serving
- **GPTQ**: Widely supported, well-understood, slightly lower quality than AWQ
- NVIDIA Minitron workflow: 1.64x vLLM throughput, 2.6x memory reduction

### vLLM (Production Standard for GPU Serving)
PagedAttention reduces KV cache waste to <4%. Supports continuous batching, tensor parallelism, native speculative decoding. 9x throughput advantage over Ollama under load.

### TensorRT-LLM
NVIDIA's compiler-optimized inference engine. EAGLE-3 integration achieves up to 2.8x speedup using target model hidden states — no separate draft model needed.

### Speculative Decoding (Now Production Standard)
Built into vLLM, SGLang, TensorRT-LLM:
- **Multi-Token Prediction (MTP)**: Adopted by DeepSeek V3; predicts multiple tokens per step
- **EAGLE-3**: Draft from target model hidden states; no separate draft model
- **TurboSpec**: Dynamic runtime adaptation of speculation parameters

### SGLang
Emerging alternative to vLLM for structured output generation and complex multi-call programs. RadixAttention for cross-request KV cache reuse.

### Deployment Options

| Option | Best For | Notes |
|---|---|---|
| **vLLM** | Production multi-user serving | PagedAttention, continuous batching |
| **Ollama** | Development, local, single-user | One-command setup, GGUF models |
| **LiteLLM Proxy** | Unified gateway across providers | One OpenAI-compatible endpoint |
| **LM Studio / Jan** | Non-technical users | GUI-first |
| **Cloud APIs** | OpenAI, Anthropic, Google, Together.ai, Groq | Managed, scalable |

**Self-hosting economics**: One fintech case study: $47K/month on cloud APIs → $8K/month (83% reduction) by moving predictable workloads to self-hosted Llama 3.3 70B.

---

## 6. LLM Evaluation

### Core Frameworks

| Tool | Strength | Use Case |
|---|---|---|
| **RAGAS** | RAG-specific metrics (faithfulness, relevancy, context precision/recall) | RAG evaluation starting point |
| **DeepEval** | Broader coverage, agent evaluation, CI/CD via Pytest | Production eval pipeline |
| **TruLens** | Deep RAG triad evaluation, RAG-specific guardrails | RAG quality monitoring |
| **Promptfoo** | Automated prompt regression testing, CI gate integration | CI/CD for prompts |

### LLM-as-Judge
A high-capability model (GPT-5, Claude Opus 4.x) evaluates target model outputs at scale. Now the primary mechanism for reference-free evaluation. Risks: positional bias, verbosity preference, self-consistency issues — mitigate with multi-judge ensembles.

### CI/CD for AI
Pattern: every PR modifying system prompts, model versions, or retrieval logic triggers a full eval suite. Deployments failing quality thresholds are blocked automatically.

**Gold-set regression**: Maintain curated input/expected-output pairs as regression baseline.

**Key metric**: Unvalidated LLM deployments exhibit hallucination rates up to 27% in production (2025 data).

---

## 7. AI Application Architecture Patterns

Modern AI applications are **async-first, streaming-first, and tool-orchestrated**.

### Backend Framework
FastAPI is the de facto Python framework for AI backends — native async support matches the I/O-bound profile of LLM calls.

### Streaming
- **SSE (Server-Sent Events)**: Standard for token streaming to clients and MCP tool communication. Works over standard HTTP, compatible with CDNs and load balancers.
- **WebSockets**: For bidirectional agent interactions requiring interrupt capability.

### Resilience Patterns
- **Timeout cascades**: embedding >500ms → fall back to keyword search; LLM >10s → return cached response
- **Circuit breakers**: Around external LLM API calls
- **Semantic caching**: Cache LLM responses by embedding similarity (GPTCache, Momento) — 30-60% cost reduction

### Cost Control Architecture
- Token-based rate limits per user/tenant
- Cost budgets enforced at gateway layer (**LiteLLM Proxy** is the dominant unified gateway)
- Model routing: cheap models for classification/routing; frontier only for complex reasoning

### Queue-Based for Long Tasks
Celery + Redis, BullMQ, or Temporal for long-running inference tasks that exceed function timeouts. Essential for agentic workflows.

---

## 8. MLOps and LLMOps

### Standard Toolchain (2026)

| Category | Tools |
|---|---|
| Experiment Tracking | MLflow, Weights & Biases (W&B — dominant for LLM fine-tuning) |
| Pipeline Orchestration | Kubeflow (enterprise), Prefect, ZenML |
| Model Registry | MLflow Model Registry, HuggingFace Hub |
| LLMOps-specific | LangSmith (LangChain ecosystem), Langfuse (open-source), Helicone, PromptFlow (Azure) |

### Monitoring and Drift Detection
- **Evidently AI**: Data drift using statistical tests (KS test, PSI)
- **Arize AI / Fiddler AI**: Production LLM monitoring, embedding drift, output quality tracking
- Types to monitor: input distribution drift, output style drift, performance drift, latency drift

### Enterprise Architecture (2026 Dominant Pattern)
Managed cloud platform (SageMaker, Vertex AI, Azure ML) for infrastructure + open-source tools (MLflow, Evidently AI) for portability and cost control.

### Continuous Training (CT)
Automated retraining triggered by drift threshold breaches. Full loop: data validation → training → evaluation → deployment → monitoring → trigger → repeat.

---

## 9. AI Security: OWASP LLM Top 10

**Prompt injection** appeared in 73% of production AI deployments in 2025.

### Top Threats and Mitigations

| Threat | Impact | Mitigation |
|---|---|---|
| **Prompt Injection** (#1) | Hijacks model behavior via malicious input | Input sanitization, output validation, sandboxed execution, least-privilege tools |
| **Indirect injection** (via tool outputs) | More dangerous than direct; comes from retrieved content | Assume injection will succeed; focus on containment |
| **Data Poisoning** | Backdoors via training data manipulation | Data provenance tracking, anomaly detection, differential privacy |
| **Model Extraction** | Reconstruct weights via systematic querying | Rate limiting, output perturbation, watermarking |
| **Memory Poisoning** (agent-specific) | Inject malicious content into agent memory | Memory input validation, TTL on stored memories |

### Guardrails Stack
- **LlamaFirewall** (Meta, open-source): Multi-layer guardrail system for agent security
- **Lakera Guard, Guardrails AI, NeMo Guardrails**: Commercial and open-source prompt firewall options
- Pipeline: Input validation → LLM call → Output validation → Policy enforcement

### Regulatory Pressure
EU AI Act high-risk AI requirements take full effect August 2, 2026 — forces measurable, auditable security controls.

---

## 10. Multimodal Development

### Vision-Language Models (VLMs)
Gemini 3.x Pro, GPT-5, Claude Opus 4.x, Qwen3-VL-235B at the frontier. Key capabilities: document understanding, chart/diagram parsing, grounded spatial reasoning, multi-image reasoning. InternVL and LLaVA successors are competitive for enterprise self-hosting.

### Audio
Native audio I/O integrated into flagship models (GPT-5 Voice, Gemini Live). Whisper remains standard for ASR in custom pipelines. TTS: ElevenLabs, Cartesia, PlayHT for production voice applications.

### Video Generation
Fastest-advancing modality in 2025-2026. Models: **Veo 3.1** (Google), **Sora 2** (OpenAI), **Kling 2.6**, **Wan 2.6**, **LTX 2**. Native audio-visual co-generation (video + dialogue + ambient sound in a single forward pass) is the defining 2026 capability.

---

## 11. AI Coding Tools (June 2026)

| Tool | Market Position | Best Use Case |
|---|---|---|
| **GitHub Copilot** | 29% workplace adoption, enterprise leader | Deep IDE integration, enterprise compliance |
| **Cursor** | 18% adoption, $2B ARR | Codebase-aware context, Composer multi-file edits, daily in-editor productivity |
| **Claude Code** | 18% adoption, 6x growth in 9 months | Complex reasoning, long-horizon agentic tasks, multi-file refactors, architecture |

**Winning workflow**: Cursor for daily editing and in-context autocomplete + Claude Code for complex multi-step tasks and architectural changes. Complementary, not competitive.

**Productivity impact**: 21-55% individual throughput increase measured across studies. Tools amplify both good and bad practices — strong engineering foundations required.

---

## 12. Frontier: Reasoning Models and Test-Time Compute

### Current Frontier Models (June 2026)

| Model | Key Strength |
|---|---|
| **Claude Opus 4.8** | #1 overall ranking; strongest for complex reasoning and agentic tasks |
| **GPT-5.4** | 1M context, $2.50/M input; multimodal reasoning flagship |
| **Gemini 3.1 Pro** | 94.3% GPQA Diamond (highest); best price-to-performance at frontier |
| **DeepSeek R1** | MIT-licensed, GRPO-trained; matches GPT-4 class; self-hostable |

### Test-Time Compute
The fundamental architectural shift of 2025-2026. Reasoning performed in hidden "thinking tokens" — dedicated compute at inference time rather than embedded purely in weights.

- Reasoning tax has shrunk from 5-20x cost premium (2025) to 2-5x (2026), with free-tier reasoning available
- **Extended Thinking** (Anthropic), **Deep Think** (Google), **o-series** (OpenAI) implement variations
- **Prompt engineering implication**: Prompts now *direct deliberation that already exists* rather than coaxing reasoning into being

### RLVR + Test-Time Compute Convergence
The most significant architectural trend: combining RLVR training (learning to verify correctness) with extended test-time compute (applying more reasoning at inference). Core mechanism behind all frontier reasoning models.

**ARC-AGI-3 (2026)**: GPT-5.4, Claude 4.6, and Gemini 3.1 all scored 0% — genuine generalizable reasoning remains an open problem.

---

## Key Practitioner Decision Table

| Decision Point | Current Best Practice |
|---|---|
| Alignment technique | DPO for preference alignment; GRPO/RLVR for reasoning tasks |
| Fine-tuning default | QLoRA unless hardware budget is unlimited |
| Fine-tune vs. prompt | Exhaust RAG + prompting first; fine-tune for narrow, format-critical tasks |
| RAG retrieval | Hybrid BM25 + dense vector + cross-encoder reranker |
| Inference serving | vLLM (production multi-user), Ollama (dev/local), LiteLLM (unified gateway) |
| Evaluation | DeepEval or RAGAS + LLM-as-Judge + Promptfoo CI/CD gate |
| AI coding tools | Cursor for daily, Claude Code for complex tasks |
| Security baseline | OWASP LLM Top 10, LlamaFirewall, output validation, least privilege |
| Model choice | Claude Opus 4.8 (best overall), Gemini 3.1 Pro (price/perf), DeepSeek R1 (open-source) |
| Reasoning model prompting | NO explicit CoT; let the model deliberate internally |
