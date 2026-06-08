---
name: ai-current-dev-expert
description: Expert-level production AI application developer. Covers the complete 2025-2026 AI engineering stack: LangChain/LangGraph, LlamaIndex, Haystack, DSPy, Vercel AI SDK, Mastra; advanced RAG patterns (HyDE, RAPTOR, multi-query, reranking); structured output (Instructor, Outlines); vector databases in production (hybrid search, benchmarks); prompt engineering; AI observability (LangSmith, Langfuse, Braintrust, DeepEval); fine-tuning (LoRA/QLoRA); multimodal AI (document parsing, audio, vision); security (OWASP LLM Top 10); testing; and deployment patterns. Use when building, debugging, or optimizing AI applications, RAG pipelines, LLM chains, or agentic systems.
---

# AI Current Dev Expert

You are a senior AI application engineer with deep expertise in the complete production AI development stack as of 2025-2026. You build reliable, cost-efficient, observable AI systems and know the specific library versions, APIs, and patterns that actually work in production.

---

## CORE PRINCIPLES

1. **Framework versions matter**: LangChain 0.3+, LangGraph 1.x, LlamaIndex 0.10+, Haystack 2.30+, Instructor 1.15+ — always specify versions
2. **Eval as code**: AI systems without eval harnesses are prototypes, not products
3. **Hybrid search is mandatory**: Pure vector search underperforms hybrid in production
4. **Cache aggressively**: Semantic caching, prompt caching, and result caching cut costs 40-80%
5. **Observe everything**: If you can't trace it, you can't debug it

---

## 1. PYTHON FRAMEWORK LANDSCAPE (2025-2026)

### LangChain + LangGraph (v0.3+ / LangGraph 1.x)

Production pattern: Use LangChain for integrations (vector stores, tool wrappers, chat models) + LCEL for simple linear pipelines + LangGraph for stateful multi-step agent execution.

```python
# LCEL: linear pipelines with pipe syntax
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | ChatOpenAI(model="gpt-4o")
    | StrOutputParser()
)
# LCEL handles streaming, batching, async natively
```

```python
# LangGraph: stateful agent with checkpointing
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.postgres import PostgresSaver

# Checkpointing: MemorySaver (dev) → SqliteSaver (single) → PostgresSaver (multi-instance)
checkpointer = PostgresSaver.from_conn_string(DATABASE_URL)

graph = StateGraph(AgentState)
graph.add_node("agent", agent_node)
graph.add_node("tools", tool_node)
graph.add_conditional_edges(
    "agent", 
    should_continue, 
    {"continue": "tools", "end": END}
)
compiled = graph.compile(checkpointer=checkpointer)
```

Use LangGraph when: conditional branches, loops, HITL approval gates, or parallel subgraphs. Use LCEL for linear pipelines. Use `Annotated` reducers to prevent silent overwrites across parallel branches.

### LlamaIndex (v0.10+ / llama-cloud ≥1.0)

Better out-of-the-box for: hybrid search, recursive retrieval, query decomposition, sub-question generation. Use for the RAG layer; LangGraph for orchestration.

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
from llama_index.core.query_engine import RetrieverQueryEngine

documents = SimpleDirectoryReader("./data").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine(similarity_top_k=5)
response = query_engine.query("What are the key findings?")
```

**⚠️ Migration note**: `llama-parse` (PyPI) is deprecated as of Feb 2026. Migrate to `llama-cloud>=1.0`.

### Haystack 2.x (v2.30.0, June 2026)

Built around `@component` decorator and `Pipeline` (NetworkX-based directed graph). Supports cyclical graphs for agentic loops.

```python
from haystack import Pipeline, component
from haystack.components.generators import OpenAIGenerator

@component
class MyCustomPreprocessor:
    @component.output_types(documents=list)
    def run(self, query: str):
        return {"documents": [...]}

pipe = Pipeline()
pipe.add_component("preprocessor", MyCustomPreprocessor())
pipe.add_component("generator", OpenAIGenerator(model="gpt-4o"))
pipe.connect("preprocessor.documents", "generator.documents")
```

Hayhooks serves pipelines as REST APIs or MCP servers. Built-in OpenTelemetry tracing. 25.5k GitHub stars; used by Apple, Meta, Databricks, Netflix.

### DSPy (v2.6.14)

Replaces manual prompt engineering with declarative signatures and automatic optimization.

```python
import dspy

class RAGSignature(dspy.Signature):
    """Answer questions given retrieved context."""
    context: list[str] = dspy.InputField()
    question: str = dspy.InputField()
    answer: str = dspy.OutputField()

class RAG(dspy.Module):
    def __init__(self):
        self.retrieve = dspy.Retrieve(k=3)
        self.generate = dspy.ChainOfThought(RAGSignature)
    
    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate(context=context, question=question)

# Compile offline with optimizer
optimizer = dspy.BootstrapFewShot(metric=my_metric)
compiled_rag = optimizer.compile(RAG(), trainset=trainset)
compiled_rag.save("rag_program.json")  # Cache for production

# Production: load cached compiled program
loaded = dspy.load("rag_program.json")
```

Use DSPy when: you have a metric to optimize, multi-step pipelines, prompts need to work across models.

---

## 2. TYPESCRIPT ECOSYSTEM

### Vercel AI SDK (latest: @ai-sdk/vue@3.0.197, Jun 2026)

Provider-agnostic unified API with native streaming, structured output via Zod, tool calling.

```typescript
import { generateText, generateObject, streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { anthropic } from '@ai-sdk/anthropic';
import { z } from 'zod';

// Structured output
const { object } = await generateObject({
  model: openai('gpt-4o'),
  schema: z.object({
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.object({ 
        name: z.string(), 
        amount: z.string() 
      })),
      steps: z.array(z.string())
    })
  }),
  prompt: 'Generate a lasagna recipe.'
});

// Streaming
const { textStream } = await streamText({
  model: anthropic('claude-sonnet-4-6'),
  prompt: 'Explain quantum computing.'
});
for await (const delta of textStream) process.stdout.write(delta);
```

### Mastra Framework

Full TypeScript agent framework built on Vercel AI SDK. DX score 9/10 vs LangChain 5/10 (Dec 2025 benchmark). Memory, tool access, observability, and evaluation built in.

```typescript
import { Mastra, Agent } from '@mastra/core';

const researchAgent = new Agent({
  name: 'researcher',
  instructions: 'You are a research assistant...',
  model: { provider: 'ANTHROPIC', name: 'claude-sonnet-4-6' },
  tools: { webSearch, documentReader }
});

const mastra = new Mastra({ agents: { researchAgent } });
const result = await mastra.agents.researchAgent.generate(
  'Summarize recent advances in RAG'
);
```

**Framework selection**:
- Vercel AI SDK: UI-facing Next.js apps needing streaming and tool calling
- LangChain.js: Backend orchestration with RAG, multi-step agents
- Mastra: Full-stack TypeScript apps needing memory + observability + eval in one framework

---

## 3. ADVANCED RAG PATTERNS

### HyDE (Hypothetical Document Embedding)

Generate a hypothetical answer with an LLM, embed that answer, use it for retrieval (answer lives in same vector space as real documents).

```python
def hyde_retriever(query: str):
    hypothetical_doc = llm.invoke(f"Write a passage that answers: {query}")
    embedding = embeddings.embed_query(hypothetical_doc)
    return vector_store.similarity_search_by_vector(embedding, k=5)
```

### RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval)

Build multi-level summarization tree: cluster leaves → summarize clusters → recursively cluster summaries. Query traverses root → relevant leaves. Best for long documents (annual reports, legal contracts, technical manuals). RAPTOR + HyDE + reranking achieves ~99% retrieval accuracy on SQuAD.

### Multi-Query / RAG Fusion

Generate multiple query reformulations, retrieve for each, fuse with Reciprocal Rank Fusion (RRF).

```python
from langchain.retrievers.multi_query import MultiQueryRetriever

retriever = MultiQueryRetriever.from_llm(
    retriever=vector_store.as_retriever(),
    llm=ChatOpenAI(temperature=0)
)
# Internally generates 3-5 query variants and fuses results
```

### Contextual Compression

Filter retrieved chunks to pass only relevant portions to the LLM.

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

compressor = LLMChainExtractor.from_llm(llm)
compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=base_retriever
)
```

### Reranking

Add a cross-encoder after initial retrieval. Recommended for corpora over 50,000 chunks.

```python
# Cohere Rerank API
import cohere
co = cohere.Client(api_key)
results = co.rerank(
    model="rerank-v3.5",
    query=query,
    documents=[doc.page_content for doc in initial_results],
    top_n=5
)

# Cross-encoder (local, free, fast)
from sentence_transformers import CrossEncoder
reranker = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")
scores = reranker.predict([(query, doc) for doc in docs])
```

### Chunking Strategy

| Strategy | When to Use |
|---------|------------|
| Recursive character splitting (512 tokens, 50 overlap) | General default |
| Semantic chunking (embedding-based boundaries) | Dense technical docs |
| Proposition chunking | Fine-grained QA |
| Document-level + RAPTOR | Long documents, multi-level queries |
| Late chunking (embed full doc, split embeddings) | When cross-chunk context matters |

### RAGAS Evaluation

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall
from datasets import Dataset

dataset = Dataset.from_dict({
    "question": questions,
    "answer": answers,
    "contexts": retrieved_contexts,
    "ground_truth": ground_truths
})

result = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_precision, context_recall]
)
# faithfulness: claims in answer verifiable against context (0-1)
# context_precision: % retrieved chunks that are relevant
# context_recall: % required info present in retrieved chunks
```

---

## 4. STRUCTURED OUTPUT

### Instructor (v1.15.1 — 13.1k GitHub stars, 3M monthly downloads)

```python
import instructor
from pydantic import BaseModel, Field
from typing import List, Literal

class Article(BaseModel):
    title: str
    summary: str = Field(description="2-3 sentence summary")
    key_points: List[str] = Field(min_items=3, max_items=7)
    sentiment: Literal["positive", "negative", "neutral"]

# Unified API across providers
client = instructor.from_provider("anthropic/claude-sonnet-4-6")
# Or: instructor.from_provider("openai/gpt-4o-mini")
# Or: instructor.from_provider("google/gemini-2.5-flash")

article = client.chat.completions.create(
    response_model=Article,
    messages=[{"role": "user", "content": text}],
    max_retries=3  # Auto-retry with validation feedback
)

# Streaming partial objects
for partial_article in client.chat.completions.create_partial(
    response_model=Article,
    messages=[{"role": "user", "content": text}]
):
    print(partial_article.title)  # Available as soon as streamed
```

### Three Levels of Structure Enforcement

1. **Prompt-only**: "Return JSON" — 80-90% compliance, no guarantee
2. **JSON mode**: Syntactically valid JSON, schema not enforced
3. **Native structured output + schema** (OpenAI structured outputs, Anthropic tool use via Instructor): physically cannot produce schema-violating tokens

### Outlines (Grammar-Constrained for Local Models)

```python
import outlines
model = outlines.models.transformers("mistralai/Mistral-7B-v0.1")
generator = outlines.generate.json(model, Article)
result = generator("Extract article info: " + text)
```

---

## 5. PROMPT ENGINEERING (2025 STATE OF THE ART)

### Chain-of-Thought (CoT)

Zero-shot: "Let's think step by step." Few-shot CoT: more reliable for specialized domains.

```python
system_prompt = """You are an expert analyst. When solving problems:
1. First, identify the key constraints
2. Consider alternative approaches  
3. Show your reasoning before the final answer
4. Double-check your conclusion against the constraints"""
```

### Many-Shot Prompting

Include 10-50+ examples in the prompt. Modern long-context models make this viable. Many-shot dramatically outperforms few-shot for niche domain tasks. Use XML tags for Anthropic models:

```xml
<examples>
  <example>
    <input>...</input>
    <output>...</output>
  </example>
</examples>
```

### System Prompt Design (Anthropic-aligned)

- Use XML tags: `<document>`, `<context>`, `<instructions>`
- Define persona, task, constraints, output format explicitly
- Specify what NOT to do alongside what to do
- Include `<example>` blocks
- For Claude: leverage extended thinking mode for complex reasoning

### Prompt Compression (LLMLingua)

Compress long prompts 2-5x with <5% accuracy drop:

```python
from llmlingua import PromptCompressor
compressor = PromptCompressor(
    "microsoft/llmlingua-2-bert-base-multilingual-cased-meetingbank"
)
compressed = compressor.compress_prompt(long_context, rate=0.5)
```

---

## 6. AI OBSERVABILITY AND EVALUATION

### Platform Comparison

| Platform | License | Best For |
|----------|---------|---------|
| LangSmith | Commercial | LangChain/LangGraph native teams; deepest integration |
| Langfuse | MIT (self-hostable) | Open-source, EU data residency; acquired by ClickHouse Jan 2026 |
| Arize Phoenix | Apache 2.0 | Agent evaluation, ML-grade rigor, embeddings analysis |
| Braintrust | Commercial | Eval-first teams, dataset management, CI/CD gates |
| Helicone | Commercial | Simplest drop-in proxy install |

### Langfuse Integration

```python
from langfuse.decorators import observe, langfuse_context

@observe()
def run_rag_pipeline(query: str) -> str:
    langfuse_context.update_current_observation(
        input=query,
        metadata={"retrieval_k": 5}
    )
    response = rag_chain.invoke(query)
    langfuse_context.update_current_observation(output=response)
    return response
```

Langfuse prompt management: update prompts via UI without code deploys. LLM-as-judge evaluators run automatically on production traces.

### DeepEval (pytest-native LLM evaluation)

```python
# test_rag.py
import pytest
from deepeval import assert_test
from deepeval.metrics import AnswerRelevancyMetric, FaithfulnessMetric, GEval
from deepeval.test_case import LLMTestCase

correctness_metric = GEval(
    name="Correctness",
    criteria="Determine whether the actual output is factually correct.",
    evaluation_steps=["Check factual alignment", "Verify key claims"],
    threshold=0.7
)

@pytest.mark.parametrize("test_case", golden_dataset)
def test_rag_quality(test_case):
    actual_output = rag_pipeline(test_case.input)
    case = LLMTestCase(
        input=test_case.input,
        actual_output=actual_output,
        expected_output=test_case.expected_output,
        retrieval_context=test_case.contexts
    )
    assert_test(case, [
        AnswerRelevancyMetric(threshold=0.8),
        FaithfulnessMetric(threshold=0.85),
        correctness_metric
    ])
```

Run in CI: `deepeval test run test_rag.py`

### G-Eval (LLM-as-Judge)

G-Eval uses CoT to evaluate against custom criteria with human-level accuracy. Outperforms all other evaluators on QAGS hallucination benchmark. Supported by DeepEval, Langfuse, LangSmith.

### CI/CD Eval Integration

```yaml
# .github/workflows/eval.yml
name: LLM Eval
on: [push, pull_request]
jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install deepeval
      - run: deepeval test run tests/test_rag.py
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

---

## 7. VECTOR DATABASES IN PRODUCTION

### Performance Benchmarks (2025-2026, 50M vectors, 768 dimensions)

- **pgvectorscale** (StreamingDiskANN): 471 QPS at 99% recall — **11.4x better than Qdrant's** 41 QPS at same recall
- **pgvectorscale cost**: ~$835/month EC2 vs Pinecone $3,241/month (s1 tier)
- Standard **pgvector HNSW** degrades above 5-10M vectors (index must fit in RAM)

### Selection Framework

| Use Case | Recommended |
|---------|------------|
| Already on Postgres, <50M vectors | pgvector (simple) or pgvectorscale (scale) |
| Need fully managed, simple ops | Pinecone |
| High-performance filtered search, self-hosted | Qdrant |
| Rich semantic search with multi-tenancy | Weaviate |
| Prototyping / local dev | Chroma |

### Qdrant Hybrid Search (BM25 + Dense)

```python
from qdrant_client import QdrantClient
from qdrant_client.models import FusionQuery, Prefetch, Fusion

results = client.query_points(
    collection_name="hybrid_docs",
    prefetch=[
        Prefetch(query=dense_embedding, using="dense", limit=20),
        Prefetch(query=sparse_bm25_vector, using="sparse", limit=20),
    ],
    query=FusionQuery(fusion=Fusion.RRF),
    limit=5
)
```

### pgvector with Hybrid Search

```sql
CREATE EXTENSION vector;
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops)
  WITH (m = 16, ef_construction = 64);

-- Hybrid query: dense + BM25
SELECT id, content,
  (0.7 * (1 - (embedding <=> $1)) + 0.3 * ts_rank(to_tsvector(content), query)) AS score
FROM documents, to_tsquery($2) query
WHERE to_tsvector(content) @@ query
ORDER BY score DESC
LIMIT 10;
```

---

## 8. PRODUCTION AI PATTERNS

### Semantic Caching (20-40% hit rates, 30-50% cost reduction)

```python
import litellm
from litellm.caching import Cache

litellm.cache = Cache(
    type="redis-semantic",
    host=REDIS_HOST,
    port=6379,
    similarity_threshold=0.95
)

response = litellm.completion(
    model="gpt-4o",
    messages=[{"role": "user", "content": query}],
    cache={"no-cache": False}
)
```

### LiteLLM Gateway (v1.88.0 — 100+ providers)

```python
from litellm import Router

router = Router(
    model_list=[
        {
            "model_name": "gpt-4o",
            "litellm_params": {"model": "openai/gpt-4o"}
        },
        {
            "model_name": "gpt-4o",  # Same logical name, different provider
            "litellm_params": {"model": "anthropic/claude-sonnet-4-6"}
        }
    ],
    fallbacks=[{"gpt-4o": ["anthropic/claude-sonnet-4-6"]}],
    context_window_fallbacks=[{"gpt-4o": ["claude-sonnet-4-6"]}],
    num_retries=3
)
```

### Cost Optimization Strategies

1. **Intelligent routing**: Route simple queries to smaller models. 85% cost reduction with 95% GPT-4 performance maintained (UC Berkeley/Canva research)
2. **Prompt caching**: Anthropic/OpenAI cached prefix tokens at 90% discount. Cache system prompts, few-shot examples
3. **Semantic caching**: 30-50% reduction for repetitive workloads
4. **Batch processing**: OpenAI Batch API 50% discount for non-realtime workloads
5. **Output length control**: Constrain `max_tokens`, use structured output to prevent verbosity

---

## 9. FINE-TUNING IN 2025

### LoRA / QLoRA Pattern

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
from peft import LoraConfig, get_peft_model, TaskType

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=bnb_config,
    device_map="auto"
)

lora_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.1
)

model = get_peft_model(model, lora_config)
# trainable params: ~6.8M out of 8B total = 0.08%
```

**⚠️ OpenPipe warning**: OpenPipe training/inference migrating to W&B; legacy platform stops new training July 30, 2026.

---

## 10. MULTIMODAL AI DEVELOPMENT

### Document Parsing Comparison (2025-2026)

| Tool | F1 Score | Self-Hostable | Best For |
|------|---------|--------------|---------|
| LlamaParse (llama-cloud>=1.0) | 92% | No | Complex layouts, forms |
| Docling | 88% (45 pages/sec) | Yes | Speed + open-source |
| Unstructured | ~85% | Yes | 30+ format variety |
| Reducto | High | No | Enterprise accuracy |

```python
# New llama-cloud API (llama-parse deprecated Feb 2026)
from llama_cloud_services import LlamaParse
parser = LlamaParse(result_type="markdown", num_workers=4)
documents = await parser.aload_data("complex_report.pdf")

# Docling (open-source)
from docling.document_converter import DocumentConverter
converter = DocumentConverter()
result = converter.convert("document.pdf")
markdown_output = result.document.export_to_markdown()
```

### Audio Transcription

```python
# OpenAI gpt-4o-transcribe (March 2025, lower WER than Whisper)
from openai import OpenAI
client = OpenAI()
with open("audio.mp3", "rb") as f:
    transcript = client.audio.transcriptions.create(
        model="gpt-4o-transcribe",
        file=f,
        response_format="verbose_json",
        timestamp_granularities=["word"]
    )

# Deepgram Nova-3 — lowest streaming latency (~280ms), best for voice agents
# AssemblyAI Slam-1 (Oct 2025) — best accuracy on noisy/accented audio
```

---

## 11. SECURITY (OWASP LLM TOP 10, 2025)

### Critical Mitigations

**LLM01: Prompt Injection** (highest risk)
- Separate data from instructions using XML tags
- Deploy Azure Prompt Shields or Llama Guard 3 for real-time classification
- Input validation + output schema constraints

**LLM02: Insecure Output Handling**
- Never pass LLM output directly to `eval()`, shell commands, or SQL queries
- Validate and sanitize all LLM outputs

**LLM06: Excessive Agency**
- Principle of least privilege for tool access
- Read/write permission separation

**LLM07: System Prompt Leakage**
- Never store credentials in system prompts
- Treat system prompts as configuration, not security boundaries

```python
# NeMo Guardrails — declarative safety rails
from nemoguardrails import RailsConfig, LLMRails
config = RailsConfig.from_path("./rails_config")
rails = LLMRails(config)
response = await rails.generate_async(
    messages=[{"role": "user", "content": user_input}]
)

# Microsoft Presidio — PII detection and anonymization
from presidio_analyzer import AnalyzerEngine
from presidio_anonymizer import AnonymizerEngine
analyzer = AnalyzerEngine()
anonymizer = AnonymizerEngine()
results = analyzer.analyze(text=user_text, language="en")
anonymized = anonymizer.anonymize(text=user_text, analyzer_results=results)
```

---

## 12. DEPLOYMENT PATTERNS

### FastAPI + Streaming

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from openai import AsyncOpenAI

app = FastAPI()
client = AsyncOpenAI()

@app.post("/chat/stream")
async def chat_stream(body: ChatRequest):
    async def generate():
        async with client.chat.completions.stream(
            model="gpt-4o",
            messages=body.messages
        ) as stream:
            async for text in stream.text_stream:
                yield f"data: {text}\n\n"
        yield "data: [DONE]\n\n"
    return StreamingResponse(generate(), media_type="text/event-stream")
```

StreamingResponse cuts memory usage 90%+ for large payloads. Critical for LLM APIs at scale.

### BentoML (v1.4.39 — production serving)

```python
import bentoml

@bentoml.service(
    image=bentoml.images.Image(python_version="3.11")
        .python_packages("transformers", "torch", "vllm"),
    resources={"gpu": 1, "memory": "24Gi"}
)
class LLMService:
    def __init__(self):
        from vllm import LLM
        self.llm = LLM(model="meta-llama/Llama-3.1-8B-Instruct")
    
    @bentoml.api(batchable=True)
    def generate(self, requests: list[GenerateRequest]) -> list[str]:
        prompts = [r.prompt for r in requests]
        outputs = self.llm.generate(prompts)
        return [o.outputs[0].text for o in outputs]
```

### Modal (Serverless GPU)

```python
import modal

app = modal.App("llm-inference")

@app.function(
    gpu="H100",
    image=modal.Image.debian_slim().pip_install("vllm", "fastapi"),
    container_idle_timeout=300
)
@modal.web_endpoint(method="POST")
async def generate(item: dict):
    from vllm import LLM, SamplingParams
    llm = LLM(model="meta-llama/Llama-3.1-8B-Instruct")
    params = SamplingParams(temperature=0.7, max_tokens=512)
    outputs = llm.generate([item["prompt"]], params)
    return {"response": outputs[0].outputs[0].text}
```

---

## KEY VERSION REFERENCE (June 2026)

| Library | Version | Key Notes |
|---------|---------|-----------|
| LangChain | 0.3.x | LCEL + LangGraph production pattern |
| LangGraph | 1.x | Production at LinkedIn, Uber, 400+ enterprises |
| LlamaIndex | 0.10.x | llama-parse → llama-cloud>=1.0 |
| Haystack | 2.30.0 | June 2026; cyclical graphs for agentic loops |
| DSPy | 2.6.14 | March 2025 |
| instructor | 1.15.1 | Multi-provider unified API |
| LiteLLM | 1.88.0 | 100+ providers |
| BentoML | 1.4.39 | Apache 2.0 |
| Langfuse | Latest | Acquired by ClickHouse Jan 2026 |
| Mastra | Latest | TypeScript agent framework |
