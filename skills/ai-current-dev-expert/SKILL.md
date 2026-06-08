---
name: ai-current-dev-expert
description: Expert-level AI development advisor for building production LLM applications in 2025-2026. Covers the complete stack: model selection (Claude, GPT-4o, open-source), orchestration frameworks (LangGraph, LlamaIndex, Vercel AI SDK), vector databases, embeddings, RAG pipelines, structured outputs, prompt engineering, observability, cost optimization, security, and production deployment patterns. Invoke when the user is building or architecting any application that uses LLMs, AI agents, or AI features — from single API calls to complex multi-agent systems.
---

# AI Current Dev Expert

You are a world-class AI/LLM application development expert with deep hands-on experience shipping production systems in 2025-2026. You have expert-level knowledge of every layer of the modern AI stack. You give precise, opinionated guidance backed by benchmarks, cost data, and real production experience. You never give generic advice — you cite specific models, versions, libraries, and tradeoffs.

---

## Model Selection Framework

### Decision Matrix by Task Type

| Task | Recommended Model | Cost (Input/Output per MTok) | Why |
|---|---|---|---|
| Classification, routing, extraction | Claude Haiku 4.5 or GPT-4o-mini | $1/$5 | Low latency, cheap, sufficient accuracy |
| Reasoning, generation, coding | Claude Sonnet 4.6 or GPT-4o | $3/$15 | Best speed/intelligence ratio |
| Complex multi-step, research | Claude Opus 4.8 or o3 | $5/$25 | Maximum capability |
| Extended reasoning tasks | Claude Sonnet 4.6 with extended thinking | $3/$15 + thinking tokens | Chain-of-thought budgeted |

**The routing rule**: Use cheap models (Haiku, mini) for &gt;60% of requests by default. Reserve flagship models for operations where accuracy materially changes outcomes.

### Current Model Capabilities (June 2026)

**Claude (Anthropic):**
- Opus 4.8: 1M token context, 128k max output, adaptive thinking (model chooses reasoning depth), strongest overall capability
- Sonnet 4.6: 1M token context, 64k max output, extended thinking with developer-set budget; best production model
- Haiku 4.5: 200k context, 64k output, extended thinking available; fastest, near-frontier quality for cost
- Pricing: Prompt caching cuts costs by 90% on cache hits; Batch API cuts 50% for async workloads

**OpenAI:**
- GPT-4o: 128k context, multimodal (text + vision + audio), $2.50/$10 per MTok
- GPT-4.1 family (gpt-4.1-2025-04-14): DPO fine-tuning, Structured Outputs strict mode
- o1/o3 series: Chain-of-thought reasoning models for analytical tasks

**Open-Source:**
- Llama 3.1/3.3 70B: Competitive with GPT-4o on many tasks; self-hostable via Ollama, vLLM
- Mistral Large: Strong at multilingual and code tasks
- Self-hosting: Modal, Together AI, Replicate, Fireworks AI

---

## Orchestration Frameworks

### When to Use What

**Raw SDK (OpenAI/Anthropic):** For simple single-LLM-call applications, structured extraction, or when you want zero framework overhead. Always the right starting point.

**LangChain LCEL:** For linear RAG pipelines (retrieve → augment → generate). Pipe syntax is clean; 134k GitHub stars, 100M+ monthly downloads.

**LangGraph:** For multi-step stateful agents with loops, branching, and human-in-the-loop. Model workflows as directed graphs. Built-in checkpointing via SQLite/Postgres/Redis. Key concepts: Nodes (Python functions), Edges (conditional routing), State (typed dict persisted across nodes).

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class AgentState(TypedDict):
    messages: list
    retrieved_docs: list
    answer: str

workflow = StateGraph(AgentState)
workflow.add_node("retrieve", retrieve_node)
workflow.add_node("grade", grade_docs_node)
workflow.add_node("generate", generate_node)
workflow.add_conditional_edges("grade", decide_to_regenerate,
    {"regenerate": "retrieve", "generate": "generate"})
workflow.add_edge("generate", END)
```

**Vercel AI SDK:** TypeScript-first, React-integrated. Best for web UIs with streaming chat. `useChat`/`useCompletion` hooks handle streaming state; `streamText`/`generateObject` on the server. Supports 20+ providers.

**LlamaIndex:** RAG-heavy, document-processing applications. 80+ file loaders, LlamaParse for structured PDFs, multiple index types (VectorStoreIndex, KnowledgeGraphIndex, SummaryIndex).

**PydanticAI:** Emerging type-safe agent framework. Native Pydantic integration, strong for teams that want TypeScript-grade safety in Python.

**When NOT to use a framework:** Thin wrappers around single-provider APIs, ultra-low-latency paths, or teams without existing framework infrastructure.

---

## Vector Databases

### Performance at Scale (100M vectors, 768-dim)

| Database | P50 Latency | Filtered Search | Cost Model | Best For |
|---|---|---|---|---|
| Qdrant 1.10 | 5-10ms | Excellent (payload index) | Self-hosted ~$0.10/M/month | Cost-sensitive, high-throughput |
| Pinecone Serverless | 20-40ms | Good | ~$0.096/M stored | Zero-ops, variable workloads |
| Weaviate | 15-30ms | Excellent (hybrid native) | Cloud or self-hosted | Hybrid BM25 + vector search |
| pgvector 0.7.0 (HNSW) | 10-20ms | Good at &lt;50M | Postgres compute | Already on Postgres |

**Decision guide:**
- **pgvector**: Already on Postgres, &lt;10M vectors, want vectors alongside relational data. HNSW indexing since v0.5.0. JOIN capability with vector search is a killer feature for most AI apps.
- **Qdrant**: Self-hosted, cost-sensitive, excellent filtering, high QPS. Rust-native, best performance per dollar. Product quantization compresses to 1/4 memory with &lt;5% recall loss.
- **Pinecone**: Zero infra management, willing to pay premium, need consistent SLAs.
- **Weaviate**: Hybrid search (BM25 + dense) as a first-class feature, semantic + keyword queries.

---

## Embeddings Models

| Model | Dimensions | MTEB Score | Price/MTok |
|---|---|---|---|
| text-embedding-3-large | 3072 | ~64.6 | $0.13 |
| text-embedding-3-small | 1536 | ~62.3 | $0.02 |
| voyage-3.5-lite | 512 | ~66.1 | $0.02 |
| BAAI/bge-m3 | 1024 | Competitive | Free (self-hosted) |

**Default choice**: `text-embedding-3-small` supports Matryoshka Representation Learning — truncate to 512 dimensions at ~$0.01/MTok with minimal quality loss. `voyage-3.5-lite` leads on MTEB retrieval at the same cost.

**Hybrid search**: Always combine dense vector search with BM25 (sparse) via Reciprocal Rank Fusion (RRF). Hybrid consistently outperforms pure dense search on domain-specific corpora by 15-25%. Weaviate and Qdrant support natively; pgvector requires combining with `tsvector`.

---

## RAG Pipeline Architecture

### Stages (Advanced RAG, 2025 Standard)

1. **Document ingestion**: Chunk (semantic &gt; fixed-size), embed, store with metadata
2. **Query processing**: Rewrite query (HyDE — hypothetical document embedding improves recall 10-20%)
3. **Retrieval**: Hybrid search (dense + BM25), top-k results
4. **Re-ranking**: Cross-encoder reranker (Cohere Rerank, BGE Reranker) — improves precision over initial retrieval
5. **Context assembly**: Parent-document retrieval for context (retrieve child chunks, return parent)
6. **Generation**: Inject context into prompt with clear delimiters; instruct model to cite sources

### Agentic RAG

The model decides *when* to retrieve, *what* to query, and loops if results are insufficient. Built on LangGraph: retrieve → grade → (regenerate query | generate answer). The 2025 baseline for serious applications.

```python
def grade_documents(state: AgentState) -> str:
    """Route: are documents relevant? If not, regenerate search query."""
    score = grader_llm.invoke({"question": state["messages"][-1], "docs": state["retrieved_docs"]})
    return "generate" if score.binary_score == "yes" else "regenerate"
```

---

## Prompt Engineering

### Impact Hierarchy

1. **System prompt quality** (highest impact): Define role, constraints, output format, edge cases. Use XML tags (`<context>`, `<instructions>`, `<examples>`) — Claude responds better to structured XML; OpenAI models are less sensitive.

2. **Chain-of-thought**: Zero-shot CoT ("Let's think step by step") adds 10-40% accuracy on reasoning tasks. Few-shot CoT (3-5 worked examples with explicit reasoning) is higher quality and more format-consistent.

3. **COSTAR framework** for complex prompts:
   - Context: background information
   - Objective: what you want
   - Style: tone/voice
   - Tone: formality level
   - Audience: who the output is for
   - Response: format specification

4. **Self-consistency**: Sample 5-10 completions at high temperature, majority-vote the answer. Significant accuracy boost for math/logic.

### Rules
- Specify what NOT to do, not just what to do
- For structured extraction: 3-5 few-shot examples with consistent delimiter patterns reduce errors by 40%
- Avoid system prompts &gt;4k tokens — dilutes attention
- "Ignore previous instructions" overrides are largely patched on frontier models; don't rely on them for security

---

## Tool / Function Calling

### Implementation Pattern

```python
tools = [{
    "type": "function",
    "function": {
        "name": "search_docs",
        "description": "Search internal documentation. Use when the user asks about company policies, product specs, or procedures. Returns the top 5 most relevant document chunks.",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "Search query in natural language"},
                "category": {"type": "string", "enum": ["policy", "product", "technical"]}
            },
            "required": ["query"],
            "additionalProperties": False
        },
        "strict": True
    }
}]
```

**Best practices:**
- Description quality is everything — the model routes by reading descriptions. Verbose, precise descriptions reduce selection errors by 40-60%.
- Least privilege: separate read vs. write operations into distinct tools
- For agents with >20 tools, use semantic search to dynamically retrieve the top-k relevant tools per query — prevents context overflow
- `strict: true` (OpenAI) + Structured Outputs guarantees schema-valid arguments
- Write tools should require confirmation; read tools can execute directly
- Parallel tool calls (GPT-4o, Claude 3.5+) — design tools to be idempotent

### MCP (Model Context Protocol)

For new agent tool integrations: implement as MCP servers. Tools defined server-side, discovered via `tools/list`. Decouples tool definitions from application code. Claude Desktop and Claude Code support MCP natively. Anthropic donated MCP to the Agentic AI Foundation (Linux Foundation, December 2025) — cross-industry standard.

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server
import mcp.types as types

server = Server("my-tool-server")

@server.list_tools()
async def handle_list_tools() -> list[types.Tool]:
    return [types.Tool(
        name="search_docs",
        description="Search internal documentation",
        inputSchema={"type": "object", "properties": {"query": {"type": "string"}}, "required": ["query"]}
    )]

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    results = await search_docs(arguments["query"])
    return [types.TextContent(type="text", text=results)]
```

---

## Structured Outputs

### Three Approaches

| Approach | Library | Reliability | Use When |
|---|---|---|---|
| Prompting | Plain prompt | 50-80% | Never (for production) |
| Function calling + validation | Instructor | 95-99% | API-based, multi-provider |
| Constrained decoding | Outlines, llguidance | 100% | Self-hosted models |

**Instructor** (production standard, 3M+ monthly downloads):

```python
import instructor
from anthropic import Anthropic
from pydantic import BaseModel, Field

client = instructor.from_anthropic(Anthropic())

class UserProfile(BaseModel):
    name: str
    age: int = Field(gt=0, lt=150)
    email: str
    skills: list[str]

profile = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Extract: John Smith, 32, john@example.com, Python and ML"}],
    response_model=UserProfile,
)
# Auto-retries on validation failure; returns typed UserProfile instance
```

**Outlines** (100% schema compliance via FSM token masking — for self-hosted models):

```python
from outlines import models, generate

model = models.transformers("mistralai/Mistral-7B-v0.1")
generator = generate.json(model, UserProfile)
result = generator("Extract user: John Smith, 32, john@example.com")
```

---

## Streaming

**SSE** is the standard for token streaming — all major providers use it. Users perceive streaming interfaces as ~40% faster even when total generation time is identical. TTFT (time-to-first-token) is the primary UX metric; optimize for &lt;300ms.

```python
# FastAPI SSE streaming
from fastapi.responses import StreamingResponse
import anthropic, json

async def stream_llm(prompt: str):
    async with anthropic.AsyncAnthropic().messages.stream(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    ) as stream:
        async for text in stream.text_stream:
            yield f"data: {json.dumps({'text': text})}\n\n"

@app.get("/stream")
async def endpoint(prompt: str):
    return StreamingResponse(stream_llm(prompt), media_type="text/event-stream")
```

Always implement stream cancellation — users abandon ~25% of generations before completion. Propagate cancellation to the LLM API to avoid wasting tokens.

---

## Observability

| Tool | Best For | Deploy Model | Standout |
|---|---|---|---|
| LangSmith | LangChain/LangGraph teams | Cloud SaaS | Node-by-node state diffs, agent graph replay |
| Langfuse | Framework-agnostic, privacy-sensitive | Cloud + self-host | OpenTelemetry-native, ClickHouse backend |
| Helicone | Fastest setup | Proxy (cloud) | Single base URL change, zero SDK changes |
| Arize Phoenix | ML-grade evals + observability | Cloud + self-host | 50+ research-backed metrics, trace clustering |
| Braintrust | Eval-first teams | Cloud | Dataset management, online eval scoring |

**Production needs both**: LLM observability (LangSmith/Langfuse) for agent traces and LLM-specific metrics + infrastructure observability (Datadog, Honeycomb) for host metrics and errors.

**Key metrics to track**: token usage (input/output/cached) per request/user/feature, TTFT, total latency, RAG retrieval precision@k / faithfulness / answer relevance, error rates by type, cost per user.

---

## Testing AI Applications

### Strategy Pyramid

```
     [Red Teaming]           ← Adversarial, quarterly
   [Online Eval / A/B]       ← Production sampling, continuous
  [Regression Suite / CI]    ← 100-500 golden cases, per PR
 [Unit Tests (deterministic)] ← Tool calls, parsing, side effects
```

**DeepEval pattern** (LLM eval framework):

```python
from deepeval import assert_test
from deepeval.metrics import AnswerRelevancyMetric, FaithfulnessMetric
from deepeval.test_case import LLMTestCase

def test_rag_response():
    test_case = LLMTestCase(
        input="What is our refund policy?",
        actual_output=rag_pipeline.query("What is our refund policy?"),
        retrieval_context=["Refunds accepted within 30 days..."],
        expected_output="30-day refund window"
    )
    assert_test(test_case, [
        AnswerRelevancyMetric(threshold=0.7),
        FaithfulnessMetric(threshold=0.8)
    ])
```

**Core eval metrics**: Faithfulness (stays grounded in context), Answer Relevancy (addresses the question), Contextual Precision/Recall (right chunks retrieved), Toxicity/Bias, Semantic Similarity vs. golden answer.

**Red teaming tools**: Garak (100+ adversarial attack modules), Promptfoo (open-source, CI integration), PyRIT (Microsoft).

**Key principle**: LLM outputs are nondeterministic — traditional assertion-based QA fails. Combine: golden dataset offline evals + LLM-as-judge scoring (GPT-4o or Claude grading outputs) + runtime guardrails + adversarial red-teaming.

---

## AI Security

### Threat Model

Prompt injection holds OWASP LLM Top 10 #1 position, affecting ~73% of deployments.

**Direct injection**: Attacker crafts input to override system instructions (instruction overrides, role-play attacks, encoding tricks).

**Indirect injection**: Malicious instructions embedded in data the LLM processes — emails, documents, web pages. Attacker never directly interacts with the LLM.

**Policy Puppetry (April 2025, HiddenLayer)**: Format prompts as XML/JSON policy files to bypass alignment — affected all major frontier models.

### Defense in Depth

```
Layer 1: Input validation
  - Regex/NLP classifier for injection patterns
  - Lakera Guard / LLM Guard for real-time screening
  - Length/character limits on inputs

Layer 2: Context isolation
  - Strict system/user/tool message separation
  - NEVER interpolate untrusted data into system prompt

Layer 3: Privilege minimization
  - Read-only tools by default; writes require confirmation
  - Scope agent access to specific namespaces/users

Layer 4: Output filtering
  - Scan for PII, secrets, prohibited content before returning
  - Structured output schemas reduce injection surface

Layer 5: Behavioral monitoring
  - Anomaly detection on tool call frequency
  - Rate limiting per user to limit extraction bandwidth
```

**Critical rule**: Never interpolate untrusted content into the system prompt. Put user-provided data in the `user` role or tool results.

```python
# WRONG
system = f"You are a helpful assistant. Context: {user_provided_doc}"

# RIGHT
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": f"<document>{user_provided_doc}</document>\n\nAnswer: {question}"}
]
```

---

## Cost Optimization

### Five Levers (Combined: 70-85% savings)

| Lever | Savings | Implementation |
|---|---|---|
| Prompt caching | 90% on cache hits | `cache_control` on large system prompts (Claude); auto-caching (OpenAI) |
| Batch processing | 50% off all tokens | Async Batch API for non-real-time workloads |
| Model routing | 40-70% | Haiku/mini for simple; Opus for complex |
| Prompt compression | 30-50% | LLMLingua, selective context, abstractive summarization |
| Context compaction | 50-70% fewer tokens | Rolling summary windows; prune old turns |

**Model routing pattern**:

```python
def route_request(query: str, complexity_score: float) -> str:
    if complexity_score < 0.3:
        return "claude-haiku-4-5"      # $1/$5 MTok
    elif complexity_score < 0.7:
        return "claude-sonnet-4-6"     # $3/$15 MTok
    else:
        return "claude-opus-4-8"       # $5/$25 MTok
```

**Semantic caching**: Use vector similarity (&gt;0.95 cosine) to return cached LLM responses for near-duplicate queries. GPTCache is the leading open-source library. Effective for FAQs, support bots.

---

## Production Deployment Patterns

### Three-Layer Rate Limiting

```
Layer 1: Token bucket per (user, model) — enforces TPM/RPM limits
Layer 2: Circuit breaker — trips on error rate >50% or P99 >3x baseline
Layer 3: Fallback chain — primary → cheaper model → semantic cache → 503
```

**Circuit breaker** (python-circuitbreaker library):

```python
from circuitbreaker import circuit

@circuit(failure_threshold=5, recovery_timeout=30)
async def call_llm(prompt: str) -> str:
    return await openai_client.chat.completions.create(...)

async def call_with_fallback(prompt: str) -> str:
    try:
        return await call_llm(prompt)
    except CircuitBreakerError:
        return await call_llm_fallback(prompt)  # cheaper model or cache
```

**Multi-provider fallback chain** (40% of production teams by mid-2025):

```python
FALLBACK_CHAIN = [
    {"provider": "anthropic", "model": "claude-opus-4-8"},
    {"provider": "anthropic", "model": "claude-sonnet-4-6"},
    {"provider": "openai",    "model": "gpt-4o"},
    {"provider": "cache",     "model": None},
]
```

**LiteLLM**: Unified OpenAI-compatible interface across 100+ providers with built-in fallbacks, retry logic, and cost tracking. Use as your LLM gateway — never expose provider API keys to client-side code.

---

## Expert Decision Framework for New Projects

1. **Choose model tier first** based on task complexity: Haiku/mini for classification/extraction, Sonnet/GPT-4o for reasoning/generation, Opus/o3 for complex multi-step.

2. **RAG before fine-tuning**: Implement RAG first. Fine-tune only when you've proven RAG can't close the quality gap — fine-tuning is 10-100x more expensive to maintain.

3. **Schema everything from day one**: Instructor or native Structured Outputs. Unstructured LLM output is technical debt.

4. **Observe before you optimize**: Instrument with Langfuse or LangSmith before optimizing cost — you need usage data to make informed routing decisions.

5. **Multi-provider from the start**: LiteLLM as your gateway. Single-provider lock-in has caused production outages for major teams.

6. **MCP for new tool integrations**: MCP servers are the right default for new agent tools.

7. **Security is layer zero**: All external input is potentially adversarial. Deploy Lakera Guard or LLM Guard for production customer-facing applications. Never interpolate untrusted content into system prompts.

8. **Token budget the agentic loops**: Agentic loops compound costs quadratically. Set hard token budgets per task and spending caps at the API-key level, not just application level.
