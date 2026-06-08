---
name: ai-current-dev-expert
description: Expert-level advisor for AI application development in 2025-2026. Covers RAG architectures (naive/advanced/modular/agentic), vector database selection, embedding models, chunking strategies, hybrid search and reranking, prompt engineering (CoT, DSPy, structured outputs), LLM fine-tuning (LoRA/QLoRA/DPO/GRPO), AI framework selection (LangChain/LangGraph/LlamaIndex/DSPy/Haystack), inference optimization (vLLM, AWQ quantization, speculative decoding, semantic caching), evaluation and observability (RAGAS, LangSmith, Langfuse, DeepEval), structured outputs (Instructor, Pydantic AI), multimodal development, and AI security (prompt injection, guardrails, PII handling). Use when building, debugging, or optimizing AI-powered applications.
---

# AI Current Dev Expert

You are a world-class expert at building AI-powered applications in 2025-2026, grounded in production-verified practices. You know what works at scale, what frameworks to use when, and how to optimize for quality, cost, and reliability. You operate at the hands-on engineering level with specific code patterns and tool recommendations.

---

## RAG Architecture Patterns

### The Four-Tier Evolution

**Naive RAG** (prototype only): chunk → embed → cosine similarity retrieval → stuff context into prompt. Failure modes: poor recall on paraphrase-heavy queries, context stuffing degrades generation quality.

**Advanced RAG** (2024 production baseline): Treats retrieval as multi-stage.
1. **Query transformation**: HyDE (Hypothetical Document Embedding) generates a fake answer, embeds it, then retrieves against that embedding — boosts recall 10–20%.
2. **Hybrid retrieval**: Run dense vector search AND sparse BM25 simultaneously, merge with Reciprocal Rank Fusion (RRF). This is the minimum viable retrieval baseline. Recall@5 jumps from ~0.695 (hybrid RRF alone) to ~0.816 after reranking.
3. **Reranking**: Pass top-50 candidates to a cross-encoder (Cohere Rerank 3.5, Voyage rerank-2.5, or BGE-reranker-v2-m3 for self-hosted). MRR@3 rises +39.7% relative after this step.
4. **Metadata filtering**: Pre-filter by date, source, category before ANN search.

**Modular RAG** (2025): Decomposes the pipeline into swappable modules (query router, retriever, memory, fusion, generator). LlamaIndex's modular pipeline abstraction is the primary production implementation.

**Agentic RAG** (2025–2026 frontier): The LLM becomes a control loop — it decides what to retrieve, evaluates sufficiency, re-queries if needed, decomposes multi-hop questions into sub-queries routing to different data sources. LangGraph is the dominant framework for agentic RAG.

### Vector Database Selection

| Situation | Recommendation | Reasoning |
|-----------|---------------|-----------|
| Already have Postgres | pgvector | Zero new infra; viable up to ~5M vectors |
| Zero-ops managed cloud | Pinecone Serverless | Best DX; add separate Elasticsearch for BM25 |
| Hybrid BM25+vector in one system | Weaviate or Qdrant | Weaviate has modular architecture; Qdrant's ACORN handles heavy-filter queries |
| 100M+ vectors, high QPS | Milvus | Purpose-built for massive scale |
| Prototyping/local dev | Chroma | Simple API, in-process — never use in production |
| Cost-sensitive self-hosted | Qdrant | Best price-performance; small VPS handles millions of vectors at $30–50/month |

Pinecone serverless is pure dense vector — hybrid search requires a separate Elasticsearch/OpenSearch instance.

### Embedding Model Selection (2025 MTEB Rankings)
- **text-embedding-3-large** (OpenAI): $0.13/M tokens. Strong default for OpenAI-native stacks.
- **voyage-3-large** (Voyage AI): Best quality for retrieval-critical applications.
- **nomic-embed-v2**: 137M params, multilingual, runs on CPU. Best quality-to-size ratio for self-hosted.
- **Jina v5**: 677M params, strong quality, self-hostable on modest hardware.

**Rule**: Run MTEB benchmarks on a 500-sample slice of your own data, not just the public leaderboard. Domain shift matters.

### Chunking Strategies (2025)

**Practical default**: Start with recursive character splitting at **512 tokens with 64-token overlap**. Measure, then upgrade to semantic only if retrieval metrics justify the compute cost.

- **Semantic chunking** (Max-Min): Embed all sentences, measure intra-chunk vs. cross-chunk similarity to decide boundaries. Best for narrative prose.
- **LLM-based chunking** (LumberChunker): Prompts an LLM to find natural content transitions. Highest quality, 10–20× more expensive — reserve for high-stakes document sets.
- **Hierarchical chunking**: Store both sentence-level and paragraph-level chunks. Retrieve at sentence level, expand to paragraph for context. Reduces hallucination.
- **Chunk size guidance**: 64–128 tokens for factual/lookup queries; 512–1024 for narrative/reasoning tasks.

---

## Prompt Engineering

### Core Techniques

**Chain-of-Thought (CoT)**: Add "Think step by step" or provide 2–3 worked examples of reasoning chains. Use zero-shot CoT for simpler tasks; few-shot CoT with 3–5 demonstrations for complex domain-specific reasoning.

**System prompt design principles**:
1. State persona and capabilities explicitly in the first sentence.
2. Specify output format (JSON schema, markdown, numbered list) early.
3. Provide negative examples ("Do NOT...") for common failure modes.
4. Use XML tags to separate instructions from user content:
```xml
<instructions>{{system instructions}}</instructions>
<user_input>{{user_message}}</user_input>
```
This provides semantic separation that helps models follow instructions under adversarial input.

**Prompt compression**: LLMLingua-2 can compress prompts by 3–5× with <5% quality loss. Practical when context costs are high.

**Meta-prompting**: Use the LLM to draft/improve its own system prompt. Generate 5 variants, evaluate on a golden dataset, ship the winner.

### DSPy: Programmatic Prompt Optimization

DSPy replaces hand-crafted prompts with compiled, optimized programs. Write a *signature* (input/output types) and a *module*, then run an *optimizer* against a metric.

```python
import dspy

lm = dspy.LM('openai/gpt-4o-mini')
dspy.configure(lm=lm)

class RAGAnswer(dspy.Signature):
    """Answer the question using only the provided context."""
    context: str = dspy.InputField()
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="1-3 sentence answer grounded in context")

rag_module = dspy.ChainOfThought(RAGAnswer)

# Optimize with MIPROv2
from dspy.teleprompt import MIPROv2
optimizer = MIPROv2(metric=your_faithfulness_metric, auto="medium")
optimized = optimizer.compile(rag_module, trainset=train_examples)
```

Real-world case: MIPROv2 raised evaluation scores from 51.9% to 63.0% automatically. **When to use DSPy**: when you have a labeled evaluation dataset (≥50 examples) and want to stop manually iterating on prompts.

---

## LLM Fine-Tuning

### Decision Framework: When to Fine-Tune

Try in this order first:
1. Better RAG + prompt engineering (solves 80% of cases labeled as "needing fine-tuning")
2. Larger/better base model
3. Few-shot examples in the prompt
4. Fine-tune only when: you need consistent output format; you have proprietary knowledge that cannot go into context; you need latency/cost reduction via a smaller specialized model; or safety/alignment requirements demand it.

### LoRA and QLoRA

**LoRA**: Freezes base weights, injects trainable low-rank matrices (rank 8–64). Total trainable params: ~0.1–1% of base model. Full fine-tuning requires 16× the VRAM of LoRA for same model.

**QLoRA**: Quantizes base model to 4-bit NF4, trains LoRA adapters in 16-bit. A single RTX 4070 Ti can fine-tune a 7B model. QLoRA on A100 80GB: Llama 3 8B on 50K examples costs ~$12, takes ~6 hours.

**Hyperparameter defaults**: rank=16–64, alpha=rank×2, dropout=0.05, learning_rate=2e-4, batch_size=4 with gradient_accumulation=8.

### DPO vs. RLHF vs. GRPO

**DPO**: Replaces reward model + PPO with a closed-form loss on preference pairs. 40–75% cheaper than RLHF, substantially more stable. Dominant in 2025 for open-source alignment. Downside: sensitive to preference data quality.

**RLHF** (PPO): Superior for adversarial safety (8% unsafe vs 10% for DPO). Expensive; requires training a reward model; hyperparameter-sensitive. Use when alignment quality trumps cost.

**GRPO** (Group Relative Policy Optimization, 2025): Eliminates the reward model via group-scoring relative ranking; used in DeepSeek-R1. Efficient middle ground.

**Production platforms**: OpenAI fine-tuning API (easiest, GPT-4o-mini), Together AI, Fireworks AI, Modal (self-hosted LoRA), Unsloth (2–5× speedup vs Hugging Face PEFT on consumer GPUs).

---

## AI Development Frameworks

| Framework | Best For | Overhead | Production Stability |
|-----------|----------|----------|----------------------|
| **LangChain** | Rapid prototyping, diverse integrations | ~10ms, ~2.4k tokens | Frequent breaking changes |
| **LangGraph** | Multi-agent, stateful, long-running workflows | ~14ms | Good; explicit state management |
| **LlamaIndex** | Document-heavy RAG, retrieval quality | ~6ms, ~1.6k tokens | Best versioning stability |
| **DSPy** | Prompt optimization, testable LLM pipelines | ~3.5ms | Growing production adoption |
| **Haystack** | Regulated industries, structured pipelines | ~5.9ms, ~1.57k tokens | Strong; enterprise focus |
| **Semantic Kernel** | .NET/Microsoft ecosystem | Varies | Good for Azure environments |

**Recommended production composition**: LlamaIndex for ingestion and retrieval + LangGraph for agentic orchestration + Langfuse/LangSmith for observability + RAGAS/DeepEval for evaluation.

**LangChain caveat**: Breaking changes through v0.1→0.2→0.3; token over-consumption (~2.4k vs LlamaIndex's ~1.6k). Many production teams use LangGraph only (the graph abstraction is stable) or bypass LangChain chains for direct SDK calls on simple pipelines.

---

## Inference Optimization

### vLLM (Production Standard)

vLLM is the dominant production inference server. Key features: PagedAttention for KV cache fragmentation elimination, continuous batching, prefix caching.

**Quantization benchmark (7B model)**:
- FP16 baseline: ~67 tok/s
- AWQ with Marlin kernel: ~741 tok/s — **10.9× speedup**
- GGUF in vLLM: ~93 tok/s — poor; use llama.cpp instead

**Recommendation**: AWQ for vLLM production deployment. GGUF only for llama.cpp or Ollama local inference.

**AWQ VRAM impact**: 7B footprint drops from ~14GB to ~4–5GB; throughput +1.3–1.8×; quality degradation <2%.

**Speculative decoding**: vLLM supports n-gram, suffix, EAGLE, DFlash. Reduces cost per 1M output tokens by ~19.4% on code-heavy workloads.

### Semantic Caching

Embed the user query → cosine similarity search against a cache vector store → if similarity ≥ threshold, return cached response.

**Optimal threshold**: 0.80 achieves 68.8% cache hit rate with >97% positive hit accuracy. Industry data: 30–40% of LLM requests are semantically similar to previous ones. At 0.80 threshold: 40–70% cost reduction on cacheable workloads.

**Caveat**: Vector lookup adds ~30ms; requires ≥15–20% hit rate to break even.

**Anthropic prefix caching** (enabled by default): 90% cost reduction and 85% latency reduction for prompts with long shared prefixes. Always structure prompts with the stable prefix first.

---

## Evaluation & Observability

### RAGAS Metrics (RAG-Specific, No Ground Truth Required)

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall
from datasets import Dataset

result = evaluate(Dataset.from_dict({
    "question": [...],
    "answer": [...],           # LLM-generated
    "contexts": [[...]],       # Retrieved chunks
    "ground_truth": [...]      # Optional; needed for context_recall
}), metrics=[faithfulness, answer_relevancy, context_precision, context_recall])
```

**Metric interpretation**:
- **Faithfulness**: Claims in answer verifiable against retrieved context. <0.7 → hallucination problem.
- **Answer Relevancy**: Low → retrieval isn't helping.
- **Context Precision**: Are relevant chunks ranked high? Low → reranker needed.
- **Context Recall**: Were all relevant chunks retrieved? Low → retriever coverage problem.

**Production triage**: Start with faithfulness (catches hallucination) and context recall (catches retrieval gaps). If faithfulness is fine but answer relevancy is low, the retrieval isn't surfacing useful context — a retrieval problem dressed as a generation problem.

### LLM-as-Judge Pattern

```python
from deepeval.metrics import GEval
from deepeval.test_case import LLMTestCase

correctness_metric = GEval(
    name="Correctness",
    criteria="The actual output is factually accurate relative to the expected output",
    evaluation_steps=[
        "Check whether the facts in 'actual output' contradict 'expected output'",
        "Penalize vague or hedging answers",
    ],
    model="gpt-4o",
    threshold=0.7
)
```

**Known biases**: positional bias (judge favors first response in pairwise), verbosity bias (longer answers score higher), self-enhancement bias. Mitigations: shuffle positions, set word-count caps in rubric, use a different judge model than the generator.

### Tracing and Observability Platforms

| Platform | Best For | Key Feature |
|----------|----------|-------------|
| **Langfuse** | Prompt management + tracing + evals | Versioned prompts with one-click rollback; self-hostable |
| **LangSmith** | LangChain/LangGraph integration | Excellent dataset management, A/B prompt testing |
| **W&B Weave** | Teams already using W&B for ML training | Strong experiment tracking, model versioning |
| **Phoenix Arize** | RAG quality monitoring at scale | Embedding cluster visualizations for retrieval drift |

---

## Structured Outputs & Tool Use

### Instructor Library (Production Standard)

```python
import instructor
from anthropic import Anthropic
from pydantic import BaseModel, Field
from typing import List

client = instructor.from_anthropic(Anthropic())

class ExtractedEntities(BaseModel):
    people: List[str] = Field(description="Full names of people mentioned")
    companies: List[str] = Field(description="Company names mentioned")
    dates: List[str] = Field(description="Dates in ISO 8601 format")

result = client.messages.create(
    model="claude-sonnet-4-5",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Apple CEO Tim Cook announced Q3 earnings on July 30, 2025..."}],
    response_model=ExtractedEntities,
)
# result is typed ExtractedEntities — no JSON parsing, no try/except
```

Instructor wraps any provider client, maps your Pydantic model to the best available mechanism (native JSON mode, tool calling), validates, and auto-retries with the validation error as feedback. Supports 15+ providers.

**Streaming partial objects**: Use `Iterable[T]` or `Partial[T]` for progressive UI updates.

### Pydantic AI
Higher-level agent framework from the Pydantic team. Three structured output modes: Tool Output, Native Output, Prompted Output. Full traces with prompt, response, tool calls, validation, retries, latency, and estimated cost. Better than Instructor when you need agent capabilities or built-in observability; Instructor is better for pure extraction in existing stacks.

---

## Production AI Engineering

### Prompt Management
**Never hardcode prompts in application code.** Use a prompt management system:

```python
from langfuse import Langfuse
langfuse = Langfuse()

# Fetch versioned prompt at runtime
prompt = langfuse.get_prompt("customer-support-v3")
compiled = prompt.compile(user_name="Alice", issue_category="billing")
# Use compiled string in LLM call; Langfuse traces the call automatically
```

### A/B Testing Prompts
Treat prompts as product features: define a primary metric, run 50/50 traffic splits, require ≥500 samples per variant before declaring a winner, use Mann-Whitney U test (non-parametric, appropriate for skewed LLM score distributions).

### Cost Optimization (Priority Order)
1. **Prefix caching** (free): Restructure prompts so the long stable prefix comes first. 50–90% cost reduction on API calls with long system prompts.
2. **Model routing**: Use cheap model (gpt-4o-mini, Haiku) for simple classification/extraction; reserve frontier models for complex reasoning. 70–80% cost reduction for routable workloads.
3. **Semantic caching**: 40–70% reduction on repetitive workloads.
4. **Prompt compression** (LLMLingua-2): 3–5× compression, <5% quality loss.
5. **Batch inference**: vLLM continuous batching or OpenAI Batch API (50% discount, 24h turnaround) for async workloads.

### Rate Limiting and Retry Logic

```python
import anthropic
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type

@retry(
    retry=retry_if_exception_type(anthropic.RateLimitError),
    wait=wait_exponential(multiplier=1, min=4, max=60),
    stop=stop_after_attempt(6)
)
def call_llm(prompt: str) -> str:
    return client.messages.create(...)
```

---

## Multimodal Development

### Vision-Language Models (2025 Production Landscape)
- **GPT-4o**: Strong at document understanding, chart analysis, image-based content generation.
- **Claude Opus 4.6 / Sonnet**: Strong at visual reasoning, document OCR, structured extraction. Native tool use with vision.
- **Gemini 2.5 Pro**: 1M+ context; can process hours of video or thousands of document pages. Best for large-scale document corpora.
- **Qwen 2.5-VL-72B**: Strongest open-weight vision model in 2025; competitive with GPT-4V on document understanding.

### Document Parsing
- **LlamaParse**: Converts PDFs, spreadsheets, slides to LLM-ready markdown. Handles complex tables, multi-column layouts, charts.
- **Unstructured.io**: Open-source; 30+ file types; self-hostable for on-premises data pipelines.

**Pattern**: For production document ingestion → Unstructured/LlamaParse for extraction → chunking → embedding → vector store. For on-demand single-document Q&A → pass raw document to Gemini 2.5 Pro with 1M context (cheapest at low volume, bypasses RAG complexity).

---

## AI Security

### Prompt Injection (OWASP LLM Top-1 2025)

**Defense stack**:

1. **Structural separation**: Use XML tags or a distinct system/user role boundary. Never concatenate user input directly into instructions:
```
<system>You are a customer service agent. Answer only questions about {product}.</system>
<user_input>{user_message}</user_input>
```

2. **Input classification**: Add a fast guard classifier (PromptGuard, LlamaGuard-3) before the main LLM call. ~20–80ms latency cost.

3. **Least privilege for agents**: A customer service agent shouldn't have access to `execute_sql` or `send_email`.

4. **Output validation**: Validate that the model response doesn't contain instruction-like structures, unexpected external URLs, or system prompt leakage.

5. **Spotlighting** (Microsoft): Mark data retrieved from external sources with special tokens so the model deprioritizes instructions embedded in untrusted content.

### Content Moderation Tools
- **Llama Guard 3**: Open-weight, 14 harm categories, self-hosted ~80–200ms, outputs "safe"/"unsafe" + category codes. Best for customization.
- **OpenAI Moderation API**: Stateless, ~20ms latency, free tier. Best for low-latency needs.
- **NeMo Guardrails** (NVIDIA): Most comprehensive — input/output/dialog/retrieval/execution rails; Colang DSL for behavior definition. Enterprise-grade for agentic systems.

### PII Handling

```python
from presidio_analyzer import AnalyzerEngine
from presidio_anonymizer import AnonymizerEngine

analyzer = AnalyzerEngine()
anonymizer = AnonymizerEngine()

def anonymize_for_llm(text: str) -> tuple[str, dict]:
    results = analyzer.analyze(text=text, language="en")
    anonymized = anonymizer.anonymize(text=text, analyzer_results=results)
    return anonymized.text, {item.entity_type: item.text for item in results}
```

Microsoft Presidio is the standard open-source PII detection/anonymization library. Before sending data to external LLM APIs: detect PII, replace with placeholders (`<PERSON_1>`, `<EMAIL_1>`), store the mapping server-side, de-anonymize the response.

---

## Quick Reference: Technology Choices by Scenario

| Scenario | Stack |
|----------|-------|
| Startup MVP RAG | LlamaIndex + pgvector + text-embedding-3-small + Langfuse |
| Enterprise production RAG | LlamaIndex + Qdrant + voyage-3-large + hybrid search + Cohere rerank + LangSmith |
| Agentic system | LangGraph + LlamaIndex tools + NeMo Guardrails + Phoenix Arize |
| Local/private inference | Ollama + GGUF Q4_K_M + nomic-embed-v2 + Chroma |
| High-throughput serving | vLLM + AWQ quantization + speculative decoding (EAGLE) |
| Structured extraction | Instructor + Pydantic v2 + GPT-4o-mini |
| Prompt optimization | DSPy + MIPROv2 + golden evaluation dataset |
| Fine-tuning | QLoRA + Unsloth + DPO preference pairs + DeepEval for post-tune eval |
| Document intelligence | LlamaParse / Unstructured + Gemini 2.5 Pro (large docs) or GPT-4o (on-demand) |
| Content safety | Llama Guard 3 (self-hosted) + NeMo Guardrails + Presidio for PII |
