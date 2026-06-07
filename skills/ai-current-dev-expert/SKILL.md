---
name: ai-current-dev-expert
description: Expert-level reference for building production AI applications in 2025–2026. Use this skill when asked about LLM API integration, prompt engineering, RAG architectures, fine-tuning, evaluation, production AI engineering, security, or bleeding-edge inference techniques. Covers the full stack from raw API calls to multi-agent orchestration.
---

# AI Current Development Expert

## Role & Scope

You are operating as an expert AI engineer with deep, hands-on knowledge of building production-grade AI systems in 2025–2026. You have mastered LLM APIs, advanced RAG, fine-tuning pipelines, evaluation frameworks, security, and inference optimization. Provide specific, opinionated guidance backed by current benchmarks and real implementation patterns.

---

## 1. LLM API Integration Patterns

### 1.1 Streaming (Production Default)

Streaming is now the production default. Use Server-Sent Events consumed by your backend, re-emitted to the client. Track **time-to-first-token (TTFT)** separately from total latency — TTFT is the UX-critical metric.

```python
# Anthropic streaming
import anthropic
client = anthropic.Anthropic()

with client.messages.stream(
    model="claude-opus-4-5",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Explain quantum entanglement"}],
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

Key concerns:
- Buffer tokens server-side when doing post-processing (guardrails, filtering) before re-emitting
- In event-driven architectures, route streaming token chunks to Kafka for fan-out

### 1.2 Function Calling / Tool Use

All frontier models support parallel function calling — the model may invoke multiple tools in a single turn. Wrap tool execution in a proxy layer that handles OAuth token refresh, per-user rate limiting, pagination, and error normalization. The agent loop should never see raw HTTP errors.

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get current weather for a city",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string"},
                    "units": {"type": "string", "enum": ["celsius", "fahrenheit"]}
                },
                "required": ["city"],
            },
        },
    },
]

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Weather in Tokyo?"}],
    tools=tools,
    tool_choice="auto",
)

if response.choices[0].message.tool_calls:
    for tool_call in response.choices[0].message.tool_calls:
        name = tool_call.function.name
        args = json.loads(tool_call.function.arguments)
```

### 1.3 Structured Outputs (JSON Schema)

JSON mode is fragile. Structured Outputs with JSON Schema guarantees schema compliance via constrained decoding — the 2025 standard.

```python
from pydantic import BaseModel
from openai import OpenAI

class CalendarEvent(BaseModel):
    name: str
    date: str
    participants: list[str]
    location: str | None = None

client = OpenAI()
completion = client.beta.chat.completions.parse(
    model="gpt-4o-2024-08-06",
    messages=[
        {"role": "system", "content": "Extract calendar event details."},
        {"role": "user", "content": "Meeting with Alice on Friday at 3pm in Conference Room B"},
    ],
    response_format=CalendarEvent,
)
event = completion.choices[0].message.parsed  # Typed CalendarEvent object
```

For Anthropic: use the `tool_use` pattern with a single tool as a structured output enforcer.

### 1.4 Vision Inputs

All current frontier models are natively multimodal. Best practices:
- Place images **before** the text question in the prompt array
- For Claude's Files API: upload once, reference by `file_id` in multiple requests
- Supported formats: JPEG, PNG, GIF, WebP; PDF up to 32MB/100 pages

```python
# Anthropic Files API — upload once, reference many times
with open("chart.png", "rb") as f:
    file_resp = client.beta.files.upload(file=("chart.png", f, "image/png"))
file_id = file_resp.id

message = client.beta.messages.create(
    model="claude-opus-4-5",
    max_tokens=1024,
    messages=[{"role": "user", "content": [
        {"type": "image", "source": {"type": "file", "file_id": file_id}},
        {"type": "text", "text": "Summarize the key trends in this chart."},
    ]}],
    betas=["files-api-2025-04-14"],
)
```

---

## 2. Prompt Engineering Mastery

### 2.1 Few-Shot Best Practices

Select examples that are:
1. Maximally diverse (cover edge cases)
2. Representative of the target distribution
3. Correctly labeled (errors in few-shot examples propagate)

For dynamic few-shot: retrieve examples from a vector store using the query as the search input — dramatically outperforms static examples for varied-input systems.

### 2.2 Chain-of-Thought Variants

- **Zero-shot CoT**: Append "Let's think step by step." — works on decomposable reasoning tasks
- **Few-shot CoT**: Provide worked examples with reasoning traces — best for math, logic, code
- **Self-Consistency**: Sample N CoT paths at temperature=0.7, take majority vote (accuracy lift: ~17% on mathematical reasoning)
- **Tree-of-Thoughts (ToT)**: Explore multiple reasoning branches in parallel, prune by evaluation
- **Least-to-Most**: Decompose into sub-problems, solve sequentially, compose

```python
def self_consistent_answer(question: str, n_samples: int = 5) -> str:
    answers = []
    for _ in range(n_samples):
        response = client.chat.completions.create(
            model="gpt-4o",
            messages=[
                {"role": "system", "content": "Think step by step, then state your final answer."},
                {"role": "user", "content": question},
            ],
            temperature=0.7,
        )
        answers.append(extract_final_answer(response.choices[0].message.content))
    return Counter(answers).most_common(1)[0][0]
```

### 2.3 System Prompt Design Hierarchy

Structure system prompts in this order:
1. **Role definition** — who the model is, its context
2. **Capability declaration** — what it can and cannot do
3. **Tool inventory** — available tools with brief descriptions
4. **Behavioral constraints** — the "never do" list (numbered rules = hard constraints)
5. **Output format specification** — exact format requirements
6. **Grounding data** — static context (company info, user profile)

### 2.4 ReAct Framework

ReAct (Reason + Act) is the foundation of production agents. Loop: **Think → Select Tool → Observe Result → Think → ... → Final Answer**.

LangGraph and LlamaIndex Workflows are the 2025-standard orchestration layers for ReAct agents — they handle turn management, tool dispatch, and state persistence.

---

## 3. Retrieval-Augmented Generation (RAG)

### 3.1 Architecture Tiers

**Naive RAG (baseline, avoid in production):**
1. Chunk documents (fixed-size, ~512 tokens, 20% overlap)
2. Embed chunks
3. Store in vector database
4. At query time: embed query, find top-k, stuff into context

Fails in production because: single-hop retrieval misses multi-document reasoning, fixed chunking loses context, keyword-heavy queries underperform.

**Advanced RAG (production standard):**

**HyDE (Hypothetical Document Embeddings)** — generate a hypothetical answer, embed it, retrieve against real docs. Dramatically improves recall for sparse queries.

```python
def hyde_retrieve(query: str, top_k: int = 5) -> list:
    hyp_doc = llm.generate(f"Write a paragraph that answers: {query}")
    hyp_embedding = embed(hyp_doc)
    return vector_store.search(hyp_embedding, top_k=top_k)
```

**RAG-Fusion** — generate N query reformulations, retrieve for each, merge via Reciprocal Rank Fusion (RRF):

```python
def rag_fusion(query: str, n_variants: int = 4) -> list:
    variants = generate_query_variants(query, n=n_variants)
    all_results = [vector_store.search(embed(v), top_k=10) for v in variants]
    return reciprocal_rank_fusion(all_results)

def reciprocal_rank_fusion(result_lists, k=60):
    scores = {}
    for results in result_lists:
        for rank, doc_id in enumerate(results):
            scores[doc_id] = scores.get(doc_id, 0) + 1 / (k + rank + 1)
    return sorted(scores, key=scores.get, reverse=True)
```

**Reranking (essential):** After initial retrieval (top-50), apply a cross-encoder reranker (BGE-Reranker-v2, Cohere Rerank API) to re-score and select top-5. Cross-encoders process query and document jointly — far more accurate than bi-encoders.

**Contextual Compression:** Before stuffing chunks into context, extract only sentences relevant to the query. Reduces noise and token count.

### 3.2 GraphRAG

Microsoft's GraphRAG builds a knowledge graph over your corpus. Enables:
- Global summarization queries ("What themes appear across 10,000 documents?")
- Multi-hop reasoning ("Who works with Alice who also worked on Project X?")

Use the `graphrag` library. Tradeoff: indexing is 10–50x more expensive than vector RAG.

### 3.3 Agentic RAG

An autonomous agent controls retrieval: plans which sources to query, issues multiple queries, evaluates intermediate results, decides whether to retrieve more, synthesizes final answer.

**Anthropic's multi-agent research system outperformed single-agent approaches by 90.2%** in complex research tasks. LangGraph is the 2025 standard for orchestrating agentic RAG.

### 3.4 Parent-Child Chunking

Store small child chunks for precision retrieval; return full parent section for context richness:

```python
from llama_index.node_parser import HierarchicalNodeParser, get_leaf_nodes

parser = HierarchicalNodeParser.from_defaults(chunk_sizes=[2048, 512, 128])
nodes = parser.get_nodes_from_documents(documents)
leaf_nodes = get_leaf_nodes(nodes)
# Index leaf nodes for retrieval; return parent context at inference time
```

---

## 4. Fine-Tuning and Model Customization

### 4.1 When to Fine-Tune vs. Prompt Engineer

| Situation | Recommendation |
|-----------|----------------|
| New domain knowledge | RAG first — faster, updatable |
| Style/tone/format consistency | Fine-tune — hard to replicate with prompts |
| Latency-critical, expensive prompts | Fine-tune smaller model |
| Task-specific capability gap | Fine-tune (e.g., structured extraction) |
| Fast-changing data | RAG — fine-tuning can't keep up |
| Compliance / on-prem requirement | Fine-tune open model |

**Rule of thumb**: Exhaust prompt engineering and RAG before fine-tuning. Quality of data matters far more than quantity — 1,000 high-quality examples often outperform 100,000 mediocre ones.

### 4.2 LoRA / QLoRA

**LoRA**: Freezes base model weights, injects trainable rank-decomposition matrices into attention layers. Trains ~0.1–1% of parameters. No inference overhead (adapters merge at serving time).

**QLoRA**: Quantizes base model to 4-bit (NF4), adds LoRA adapters in 16-bit. Enables Llama-3.1-8B fine-tuning on a single 24GB GPU.

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(
    r=16,            # Rank — higher = more capacity
    lora_alpha=32,   # Scaling (typically 2*r)
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM",
)
model = get_peft_model(model, config)
model.print_trainable_parameters()  # ~0.5% of total
```

**2025 recommended fine-tuning stack**: QLoRA (4-bit NF4) + FlashAttention-2 + Liger Kernels + gradient checkpointing. Tools: Axolotl (flexible), Unsloth (2–5x faster), TRL's `SFTTrainer`.

### 4.3 DPO (Preferred over PPO in 2025)

DPO (Direct Preference Optimization) replaces PPO as the default alignment method — no separate reward model needed.

```python
from trl import DPOConfig, DPOTrainer

training_args = DPOConfig(
    beta=0.1,           # KL divergence penalty
    learning_rate=5e-7,
    num_train_epochs=1,
    per_device_train_batch_size=2,
)
trainer = DPOTrainer(model=model, ref_model=ref_model, args=training_args, train_dataset=dataset)
trainer.train()
```

**SimPO (2025)** eliminates the reference model entirely using length-normalized reward.

---

## 5. Evaluation and Testing

### 5.1 LLM-as-Judge

Using a capable LLM to evaluate outputs. Critical for open-ended tasks where rule-based metrics fail.

```python
JUDGE_PROMPT = """Evaluate this RAG response.

Question: {question}
Retrieved Context: {context}
Response: {response}

Score (1-5 each):
1. FAITHFULNESS: Does the response only use information from context?
2. RELEVANCE: Does it answer the question?
3. COMPLETENESS: Does it capture all relevant context?

Return JSON: {"faithfulness": N, "relevance": N, "completeness": N, "reasoning": "..."}"""
```

**Judge bias mitigations:**
- Swap response order to detect position bias
- Use multiple judge models and average scores
- Validate judge on human-labeled golden set before trusting at scale
- Avoid using the same model family for both generation and judging

### 5.2 RAGAS Framework

Four core RAG metrics computable without human labels:
- **Context Precision**: What fraction of retrieved context is relevant?
- **Context Recall**: Does retrieved context contain all necessary information?
- **Faithfulness**: Is the answer grounded in retrieved context?
- **Answer Relevance**: Does the answer address the question?

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall

result = evaluate(Dataset.from_dict(data), metrics=[
    faithfulness, answer_relevancy, context_precision, context_recall
])
```

### 5.3 CI/CD Eval Gates

Gate deployments: if average faithfulness drops below 0.80 vs. baseline, block the PR.

```yaml
# .github/workflows/eval.yml
- name: Run LLM regression tests
  run: |
    python -m pytest tests/llm_evals/ \
      --eval-model gpt-4o \
      --threshold 0.85 \
      --dataset golden_set_v2.jsonl
```

---

## 6. Production AI Engineering

### 6.1 Latency Optimization

- Use streaming (immediate perceived responsiveness)
- **Anthropic Prompt Caching**: 85% latency reduction on long system prompts
- **OpenAI Automatic Caching**: 50% cost reduction for >1024 token prompts
- Route simple queries to smaller/faster models (Haiku 3.5, GPT-4o-mini)

```python
# Anthropic prompt caching
message = client.messages.create(
    model="claude-opus-4-5",
    max_tokens=1024,
    system=[{
        "type": "text",
        "text": LARGE_STATIC_KNOWLEDGE_BASE,  # 50K tokens
        "cache_control": {"type": "ephemeral"},
    }],
    messages=[{"role": "user", "content": user_query}],
)
```

### 6.2 Semantic Caching

Enterprise LLM queries have ~31% semantic duplicate rate. Semantic caching intercepts queries, embeds them, finds near-identical past queries (cosine similarity >0.95), and returns cached responses.

**Verified 40–70% cost reduction at scale.** Deploy at the API gateway layer, not per-application.

### 6.3 Cost Management

- **Model routing**: Tier-1 (factual) → Haiku/GPT-4o-mini; Tier-2 (reasoning) → Sonnet/GPT-4o; Tier-3 (expert) → Opus/o3
- **Batch processing**: OpenAI Batch API (50% discount) for non-real-time workloads
- **Output length control**: Set `max_tokens` aggressively; use structured outputs (shorter than prose)

### 6.4 Resilient LLM Calls

```python
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(stop=stop_after_attempt(3), wait=wait_exponential(min=1, max=10))
async def resilient_llm_call(messages: list, model: str = "gpt-4o"):
    try:
        return await openai_client.chat.completions.create(
            model=model, messages=messages, timeout=30
        )
    except openai.RateLimitError:
        return await anthropic_client.messages.create(
            model="claude-sonnet-4-5", messages=messages
        )
    except openai.APITimeoutError:
        return get_fallback_response(messages)
```

Pattern: primary provider → secondary provider → cached response → graceful degradation. Never let LLM failures surface as 500 errors.

---

## 7. Context Window Management

### 7.1 Chunking Strategy Selection

| Strategy | Best For | Chunk Size |
|----------|----------|------------|
| Fixed-size | General purpose | 512–1024 tokens, 10–20% overlap |
| Sentence-boundary | Narrative, prose | Variable, preserve sentences |
| Header-based (Markdown) | Documentation, wikis | Full section under header |
| Code-aware | Source code | Function/class boundaries |

**RecursiveCharacterTextSplitter** (LangChain) is the 2025 default — tries paragraph → sentence → word boundaries in order.

### 7.2 Context Compression

When retrieved context exceeds budget:
1. **Extractive**: Cross-encoder scores each sentence for relevance; keep top-N
2. **LLM-based**: "Extract only the relevant sentences from this passage"
3. **LLMLingua-2**: Token-level compression via small LM — up to 20x compression with <5% performance loss

---

## 8. Embeddings and Vector Search

### 8.1 Embedding Model Selection (2025–2026)

| Model | MTEB Score | Dims | Notes |
|-------|------------|------|-------|
| NV-Embed-v2 (NVIDIA) | 72.3 | 4096 | Benchmark leader, NVIDIA hardware |
| Cohere embed-v4 | 65.2 | 1536 | Best commercial, multimodal |
| OpenAI text-embedding-3-large | 64.6 | 3072 | Flexible dimensionality (MRL) |
| E5-mistral-7b | 64.9 | 4096 | Strong OSS generalist |
| BGE-M3 (BAAI) | 63.0 | 1024 | **Best OSS default**, tri-mode |

**BGE-M3** supports dense, sparse (SPLADE-like), and multi-vector (ColBERT-like) retrieval from a single model — the OSS default for production RAG.

**OpenAI text-embedding-3** supports Matryoshka Representation Learning (MRL) — truncate from 3072 to 256 dimensions with modest quality loss for 4x smaller indexes.

### 8.2 Hybrid Search (Production Standard)

Pure semantic misses exact keyword matches. Pure BM25 misses semantic meaning. Hybrid beats either by 5–15%.

```python
# Qdrant hybrid search: dense + sparse
results = client.query_points(
    collection_name="documents",
    prefetch=[
        models.Prefetch(query=dense_embedding, using="dense", limit=20),
        models.Prefetch(query=SparseVector(indices=sparse_indices, values=sparse_values), using="sparse", limit=20),
    ],
    query=models.FusionQuery(fusion=models.Fusion.RRF),
    limit=5,
)
```

Always use hybrid search + cross-encoder reranking in production.

---

## 9. AI Coding Tools and Workflows (2025–2026)

### 9.1 Tool Landscape

**IDE Agents:**
- **Cursor**: Deepest codebase-awareness via `@codebase`, multi-file editing, custom `.cursorrules`
- **GitHub Copilot**: Copilot Coding Agent (May 2025) accepts GitHub Issues and autonomously implements them

**CLI Agents:**
- **Claude Code**: claude-opus-4-8 achieves 88.6% on SWE-Bench Verified; Dynamic Workflows splits jobs across parallel subagents
- **Aider**: Open-source, git-integrated, best for autonomous multi-file refactors

**Cloud Agents:**
- **Devin**: Full autonomous engineer — reads docs, writes code, runs tests, opens PRs
- **OpenHands**: Open-source, self-hostable

### 9.2 Project Configuration

```markdown
# CLAUDE.md / .cursorrules
## Tech Stack
- Python 3.12, FastAPI, SQLAlchemy 2.0, Pydantic v2
- PostgreSQL with pgvector for embeddings
- Redis for caching, Celery for background jobs

## Conventions
- All API endpoints must have OpenAPI docstrings
- Error handling: use custom exception hierarchy in src/exceptions.py
- Tests: pytest, fixtures in conftest.py, minimum 80% coverage
- Never use synchronous DB calls in async endpoints
```

---

## 10. LLMOps

### 10.1 Prompt Versioning

Treat prompts as production artifacts. Use Langfuse or MLflow 3.0 prompt registry for versioning.

```python
from langfuse import Langfuse
langfuse = Langfuse()

prompt = langfuse.get_prompt("customer-support-v3", version=3)
compiled = prompt.compile(user_name="Alice", issue_type="billing")

generation = langfuse.generation(
    name="support-response",
    prompt=prompt,
    model="claude-opus-4-5",
    input=compiled,
    output=response.content,
)
```

### 10.2 A/B Testing

Canary deployment: route 10% traffic to Variant B (new prompt/model). Monitor 48–72 hours. Statistical significance at p<0.05 before full rollout.

### 10.3 Observability Stack

- **Langfuse** (open-source, self-hostable): traces, prompt management, evals
- **LangSmith**: native LangChain integration, strong for chain debugging
- **Braintrust**: eval-centric, strong A/B testing
- **Arize Phoenix**: model monitoring, drift detection

**Critical metrics**: TTFT, total latency, token count (input/output separately), cost/request, cache hit rate, error rate by type, output quality scores.

---

## 11. Security

### 11.1 Prompt Injection Taxonomy

**OWASP Top 10 for LLM Applications 2025** lists prompt injection as #1.

- **Direct injection**: User directly manipulates the prompt
- **Indirect injection**: Malicious content in retrieved documents, tool outputs, emails — **the most dangerous vector** (Microsoft identifies as most widely-used AI attack in 2025)

### 11.2 Defense Architecture (Layer All of These)

```python
class LLMSecurityLayer:
    def process(self, user_input: str, context: dict) -> str:
        sanitized = self.sanitize_input(user_input)
        if self.injection_classifier.predict(sanitized) > 0.8:
            return "I cannot process that request."
        messages = self.build_isolated_messages(sanitized, context)
        tools = self.get_user_permitted_tools(context["user_role"])
        response = self.llm.generate(messages, tools=tools)
        if self.pii_detector.contains_pii(response):
            response = self.redact_pii(response)
        if self.exfil_detector.detect(response):
            return "Response blocked by security policy."
        return response
```

**Key mitigations:**
- **Instruction-data separation**: Never interpolate untrusted content into the instruction portion of prompts
- **Privilege minimization**: Agents only get tools/permissions needed for current task
- **Output filtering**: PII detection (Presidio), content scanning (OpenAI Moderation API)
- **Egress filtering**: Block tool calls that attempt to send data to unexpected destinations
- **Reality check**: Sophisticated attackers bypass best-defended models ~50% of the time with 10 attempts — defense-in-depth and impact limitation matter more than injection prevention alone

---

## 12. Bleeding-Edge Inference Techniques

### 12.1 Speculative Decoding

Small draft model generates N candidate tokens → large target model verifies all N in one forward pass → accept tokens up to first mismatch.

Typical speedup: **2–3x on greedy decoding**. Supported by vLLM, SGLang, TensorRT-LLM.

### 12.2 Continuous Batching

Unlike static batching (wait to fill), continuous batching inserts new requests the moment a sequence finishes — near-100% GPU utilization.

**vLLM** pioneered this with PagedAttention — KV-cache management analogous to OS virtual memory paging.

### 12.3 Production Inference Stack (2025–2026)

- **vLLM**: Broadest model support, multimodal, production default (used by HF Inference Endpoints)
- **SGLang**: 29% higher throughput than vLLM on small models; RadixAttention for prefix-heavy workloads
- **TensorRT-LLM**: 15–30% higher throughput on H100s; complex setup, NVIDIA only

### 12.4 FlashAttention-3

FlashAttention-3 adds: asynchronous warp-specialization (overlaps compute and memory ops), FP8 support, ~2x faster than FA-2 on H100 GPUs. Essential for training and high-throughput inference.

### 12.5 KV Cache Quantization

INT8 KV cache quantization with minimal quality loss is now standard in vLLM/SGLang — enables 2x longer context windows within same GPU memory budget.

---

## Quick Reference: Key Libraries

| Domain | Library/Tool | Notes |
|--------|-------------|-------|
| LLM APIs | `openai`, `anthropic`, `litellm` | litellm = unified API across providers |
| Orchestration | LangGraph, LlamaIndex, LangChain | LangGraph for agents, LlamaIndex for RAG |
| Vector DBs | Qdrant, Weaviate, Pinecone, pgvector | Qdrant = best performance/OSS balance |
| Fine-tuning | Axolotl, Unsloth, TRL, PEFT | Unsloth for speed, Axolotl for flexibility |
| Evaluation | RAGAS, DeepEval, Braintrust, Langfuse | RAGAS for RAG-specific |
| Embeddings | `sentence-transformers`, `fastembed` | fastembert for production speed |
| Inference serving | vLLM, SGLang, TensorRT-LLM | vLLM for breadth, SGLang for speed |
| Observability | Langfuse, LangSmith, Arize Phoenix | Langfuse = open-source self-hostable |
| Security | Presidio (PII), Garak (red-team) | Layer both |

---

## Architectural Principles

1. **Context is the new code**: Quality of context window content determines output quality more than model selection
2. **Evaluate everything**: No prompt change ships without an eval gate; LLM outputs degrade silently
3. **Defense in depth for security**: No single injection defense is reliable; layer them and minimize blast radius
4. **Hybrid beats pure**: Hybrid search over pure vector; hybrid caching over pure semantic; hybrid agents over pure pipelines
5. **Observe before you optimize**: Instrument first with traces and metrics; optimize the measured bottleneck
6. **Small model + fine-tuning often beats large model + prompting**: Fine-tuned 8B model for a specific task can match GPT-4o at 1/10th cost and latency
7. **Agentic RAG supersedes pipeline RAG**: For complex knowledge tasks, agent-controlled retrieval outperforms fixed pipelines by 90%+
