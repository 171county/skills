---
name: ai-current-dev-expert
description: Expert-level AI developer advisor covering frontier model APIs (OpenAI GPT-5, Anthropic Claude, Google Gemini, Meta Llama, Mistral, xAI Grok, DeepSeek), function and tool calling patterns, structured outputs, vision and multimodal inputs, streaming completions, batch inference, embeddings and semantic search, fine-tuning workflows, context window optimization, prompt caching, RAG implementation, vector databases, inference optimization, AI observability and monitoring, LLM testing and evals, guardrails, model versioning, cost optimization, and AI-native application architecture. Current as of June 2026.
---

# AI Current Development Expert

## Overview and Expert Mandate

The LLM API market transformed dramatically between 2025 and mid-2026: prices dropped ~80%, every major provider now supports prompt caching, native tool use, structured outputs, vision, and streaming. The competitive axis shifted from raw capability to cost-per-quality, latency, and ecosystem integration. This skill provides expert-level guidance for developers building production AI systems — covering every major provider API, integration patterns, optimization techniques, and the operational practices required to run reliable AI systems at scale.

---

## 1. Frontier Model APIs and Capabilities

### Pricing Reference (June 2026)

**OpenAI:**
| Model | Input $/1M | Output $/1M | Context | Notes |
|-------|-----------|------------|---------|-------|
| GPT-5.5 | $5.00 | $30.00 | 128K | Frontier |
| GPT-5.4 | $2.50 | $15.00 | 128K | Best-value frontier |
| GPT-4o | $2.50 | $10.00 | 128K | Vision, audio, real-time |
| o3 | ~$10.00 | ~$30.00 | 200K | Reasoning |
| o3 Pro | $150.00 | ~$600.00 | 200K | Extended reasoning |
| o4-mini | ~$1.10 | ~$4.40 | 200K | Fast reasoning |

**Anthropic:**
| Model | Input $/1M | Output $/1M | Context | Notes |
|-------|-----------|------------|---------|-------|
| Claude Opus 4.6 | $5.00 | $25.00 | 200K | Max capability |
| Claude Sonnet 4.6 | $3.00 | $15.00 | 200K | Best value flagship |
| Claude Haiku 4.5 | $1.00 | $5.00 | 200K | Fast, cost-effective |

**Google:**
| Model | Input $/1M | Output $/1M | Context | Notes |
|-------|-----------|------------|---------|-------|
| Gemini 2.5 Pro | $1.25 | Variable | 2M tokens | Largest practical context |
| Gemini 2.5 Flash | $0.30 | Variable | 1M tokens | Speed-optimized |
| Gemini 2.5 Flash-Lite | $0.10 | Variable | 1M tokens | Budget option |

**Others:**
| Model | Input $/1M | Context | Notes |
|-------|-----------|---------|-------|
| Grok 4.1 Fast (xAI) | $0.20 | 2M | Cheapest frontier; built-in web search |
| DeepSeek V3.2 | $0.14 | 128K | Best price-performance ratio |
| Mistral Large | ~$3.00 | 128K | Strong multilingual |
| Command A (Cohere) | Variable | 256K | RAG-optimized |

### Provider Selection Guide

- **Complex reasoning tasks:** Claude Opus 4.7 (highest SWE-bench), GPT-5.5 (GPQA Diamond)
- **Cost-sensitive high-volume:** DeepSeek V3.2, Gemini 2.5 Flash-Lite, Grok 4.1 Fast
- **Long document analysis:** Gemini 2.5 Pro (2M context), Claude (200K context)
- **Real-time audio conversation:** GPT-4o Realtime API
- **Enterprise with existing Azure:** Azure AI Foundry (GPT-5 family, compliance)
- **Privacy-sensitive (self-hosted):** Llama 4 Scout/Maverick, Mistral Large, DeepSeek V3.2

---

## 2. Function and Tool Calling Patterns

### Core Concept

Tool calling is the mechanism by which an LLM signals it needs to invoke an external action. The model returns a structured "I want to call function X with these arguments" response; YOUR code executes it and returns the result.

### OpenAI Tool Calling Pattern

```python
import openai

client = openai.OpenAI()

tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current weather for a city. Returns temperature in Celsius and conditions.",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "City name, e.g. 'London'"},
                "units": {"type": "string", "enum": ["celsius", "fahrenheit"], "default": "celsius"}
            },
            "required": ["city"]
        }
    }
}]

response = client.chat.completions.create(
    model="gpt-5.4",
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
    tools=tools,
    tool_choice="auto"  # or "required", "none", or specific function
)

# Handle tool call response
if response.choices[0].finish_reason == "tool_calls":
    tool_call = response.choices[0].message.tool_calls[0]
    function_name = tool_call.function.name
    arguments = json.loads(tool_call.function.arguments)
    # Execute function, append result, continue conversation
```

### Anthropic Tool Use Pattern

```python
import anthropic

client = anthropic.Anthropic()

tools = [{
    "name": "search_database",
    "description": "Search the product database. Returns matching products with price and availability.",
    "input_schema": {
        "type": "object",
        "properties": {
            "query": {"type": "string", "description": "Search terms"},
            "max_results": {"type": "integer", "default": 10, "minimum": 1, "maximum": 100}
        },
        "required": ["query"]
    }
}]

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4096,
    tools=tools,
    messages=[{"role": "user", "content": "Find me wireless headphones under $100"}]
)

# Check for tool use
for block in response.content:
    if block.type == "tool_use":
        result = execute_search(block.input)
        # Continue conversation with tool result
```

### Multi-Turn Tool Loop (Complete Pattern)

```python
def run_agent_loop(messages: list, tools: list, max_turns: int = 10) -> str:
    for _ in range(max_turns):
        response = client.messages.create(
            model="claude-opus-4-6",
            max_tokens=8192,
            tools=tools,
            messages=messages
        )
        
        if response.stop_reason == "end_turn":
            # Extract final text response
            return next(b.text for b in response.content if b.type == "text")
        
        if response.stop_reason == "tool_use":
            # Execute all tool calls
            messages.append({"role": "assistant", "content": response.content})
            tool_results = []
            for block in response.content:
                if block.type == "tool_use":
                    result = dispatch_tool(block.name, block.input)
                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps(result)
                    })
            messages.append({"role": "user", "content": tool_results})
    
    return "Max turns exceeded"
```

---

## 3. Structured Outputs

### OpenAI Structured Outputs (JSON Schema enforcement)

```python
from pydantic import BaseModel
from openai import OpenAI

class ExtractedData(BaseModel):
    company_name: str
    revenue: float | None
    employees: int | None
    founded_year: int | None
    key_products: list[str]

client = OpenAI()
response = client.beta.chat.completions.parse(
    model="gpt-5.4",
    messages=[{"role": "user", "content": "Extract: " + document_text}],
    response_format=ExtractedData  # Pydantic model → JSON schema
)

data = response.choices[0].message.parsed  # Typed ExtractedData object
```

### Anthropic Structured Output (via tool use)

```python
# Use a tool to force structured output
extraction_tool = {
    "name": "extract_entities",
    "description": "Extract structured entities from the text",
    "input_schema": {
        "type": "object",
        "properties": {
            "entities": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "name": {"type": "string"},
                        "type": {"type": "string", "enum": ["person", "org", "location", "date"]},
                        "context": {"type": "string"}
                    },
                    "required": ["name", "type"]
                }
            }
        }
    }
}

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=4096,
    tools=[extraction_tool],
    tool_choice={"type": "tool", "name": "extract_entities"},
    messages=[{"role": "user", "content": text}]
)
```

---

## 4. Vision and Multimodal Inputs

### Image Analysis (OpenAI)

```python
import base64

def encode_image(path: str) -> str:
    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode()

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "Describe this dashboard and extract all metrics"},
            {"type": "image_url", "image_url": {
                "url": f"data:image/jpeg;base64,{encode_image('dashboard.jpg')}",
                "detail": "high"  # "low" for faster/cheaper; "high" for detailed analysis
            }}
        ]
    }]
)
```

### Document Understanding (Anthropic)

```python
# PDF as base64
with open("report.pdf", "rb") as f:
    pdf_data = base64.standard_b64encode(f.read()).decode("utf-8")

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4096,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "document",
                "source": {
                    "type": "base64",
                    "media_type": "application/pdf",
                    "data": pdf_data
                }
            },
            {"type": "text", "text": "Summarize the key financial metrics from this report"}
        ]
    }]
)
```

### Gemini Multimodal (Video, Audio, PDF)

```python
import google.generativeai as genai

model = genai.GenerativeModel('gemini-2.5-pro')

# Video analysis
video_file = genai.upload_file("meeting.mp4")
response = model.generate_content([
    video_file,
    "Generate a detailed meeting summary with action items"
])

# Mixed inputs in one call
response = model.generate_content([
    genai.upload_file("report.pdf"),
    genai.upload_file("chart.png"),
    "Compare the data in the PDF report with the chart"
])
```

---

## 5. Streaming Completions

### OpenAI Streaming

```python
with client.chat.completions.stream(
    model="gpt-5.4",
    messages=[{"role": "user", "content": "Write a comprehensive analysis"}]
) as stream:
    for chunk in stream:
        if chunk.choices[0].delta.content:
            print(chunk.choices[0].delta.content, end="", flush=True)
    
    final = stream.get_final_completion()
    usage = final.usage  # Token counts available after stream completes
```

### Anthropic Streaming

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=8192,
    messages=[{"role": "user", "content": "Explain quantum computing"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
    
    final = stream.get_final_message()
    # final.usage.input_tokens, final.usage.output_tokens
```

### Streaming in FastAPI (Server-Sent Events)

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()

@app.post("/chat")
async def chat(request: ChatRequest):
    async def event_generator():
        with client.messages.stream(
            model="claude-sonnet-4-6",
            max_tokens=4096,
            messages=[{"role": "user", "content": request.message}]
        ) as stream:
            for text in stream.text_stream:
                yield f"data: {json.dumps({'text': text})}\n\n"
        yield "data: [DONE]\n\n"
    
    return StreamingResponse(event_generator(), media_type="text/event-stream")
```

---

## 6. Prompt Caching

Prompt caching dramatically reduces costs for workloads with repeated system prompts or context.

### Anthropic Prompt Caching

```python
# Mark content for caching with cache_control
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=1024,
    system=[{
        "type": "text",
        "text": system_prompt_text,  # Large system prompt (min 1024 tokens)
        "cache_control": {"type": "ephemeral"}  # Cache for 5 minutes
    }],
    messages=[{"role": "user", "content": user_question}]
)

# Cache reads cost 10% of base input price
# Cache write costs 125% of base input price (amortized over multiple reads)
# Net savings: 80-90% on cached tokens after first use
```

**When to cache:**
- System prompts > 1,024 tokens
- Static reference documents included in every request
- Tool definitions (especially long schemas)
- Few-shot examples repeated across requests

### OpenAI Automatic Caching

GPT-5 family supports automatic prompt caching with no code changes:
- Cache activates automatically for prompts > 1,024 tokens
- 90% cost savings on cache reads
- Cache key = first 1,024 tokens of the prompt (must be identical)
- Cache TTL: 5-10 minutes

---

## 7. Embeddings and Semantic Search

### Embedding Models

| Model | Dimensions | Input $/1M | Max Tokens | Notes |
|-------|-----------|-----------|------------|-------|
| text-embedding-3-large (OpenAI) | 3072 (adj) | $0.13 | 8191 | Best quality |
| text-embedding-3-small (OpenAI) | 1536 | $0.02 | 8191 | Cost-efficient |
| text-embedding-ada-002 (OpenAI) | 1536 | $0.10 | 8191 | Legacy |
| Cohere Embed v4 | 1024 | $0.01 | 512 | Cheapest quality option; multimodal |
| E5-large-v2 (open-source) | 1024 | Free | 512 | Self-hosted |
| BGE-M3 (BAAI) | 1024 | Free | 8192 | Best open-source multilingual |

### Similarity Search Implementation

```python
import openai
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

def embed_texts(texts: list[str]) -> list[list[float]]:
    response = openai.embeddings.create(
        model="text-embedding-3-large",
        input=texts,
        dimensions=1536  # Can reduce dimensions with text-embedding-3 models
    )
    return [item.embedding for item in response.data]

# In production: batch embed with max 2048 inputs per request
# Store in vector database; don't compute similarity in-memory at scale
```

### Chunking Strategy

The most impactful decision in RAG is how you split documents:

**Chunking strategies:**

| Strategy | Use Case | Chunk Size |
|----------|---------|-----------|
| Fixed-size | Simple documents, uniform structure | 500-1000 tokens |
| Recursive character splitting | General text, LangChain default | 500-1000 tokens, 100 overlap |
| Semantic chunking | Variable-density text | Variable (split at topic boundaries) |
| Document structure | PDFs with sections, code files | Preserve section/function boundaries |
| Parent-child chunking | Long docs needing context | Small child (200 tok) + large parent (2000 tok) |

**Chunk overlap:** Include 10-20% overlap between adjacent chunks to prevent missing context at boundaries.

---

## 8. Vector Databases

### Comparison Matrix

| Database | Best For | Hosting | Notes |
|----------|---------|---------|-------|
| Pinecone | Production, fully managed | Cloud only | Expensive but reliable; enterprise support |
| Weaviate | Multimodal search, self-host | Cloud + self-host | Good hybrid search; HNSW + BM25 |
| Qdrant | High-performance, Rust-based | Cloud + self-host | Best filtering performance; open-source |
| Chroma | Development, prototyping | Local + cloud | Simple Python API; great for local dev |
| pgvector | Postgres users, simple scale | Self-host (Postgres) | Free; use existing Postgres; no separate infra |
| Redis Vector | Existing Redis users | Cloud + self-host | Low latency; Redis-native |
| Milvus | Enterprise scale | Self-host | Most scalable open-source; complex ops |

### Hybrid Search Pattern

Combine dense (semantic) + sparse (BM25/keyword) retrieval for best recall:

```python
from qdrant_client import QdrantClient
from qdrant_client.models import SparseVectorParams

# Configure collection with dense + sparse vectors
client.create_collection(
    collection_name="documents",
    vectors_config=VectorsConfig(
        dense=VectorParams(size=1536, distance=Distance.COSINE)
    ),
    sparse_vectors_config={
        "sparse": SparseVectorParams(index=SparseIndexParams(on_disk=False))
    }
)

# Query with both vectors; Qdrant fuses results
results = client.query_points(
    collection_name="documents",
    prefetch=[
        Prefetch(query=dense_vector, using="dense", limit=20),
        Prefetch(query=sparse_vector, using="sparse", limit=20)
    ],
    query=FusionQuery(fusion=Fusion.RRF),  # Reciprocal Rank Fusion
    limit=10
)
```

---

## 9. RAG Implementation

### Complete RAG Pipeline

```python
from openai import OpenAI
import qdrant_client as qc

def rag_query(question: str, collection: str) -> str:
    # 1. Embed the question
    question_embedding = openai_client.embeddings.create(
        model="text-embedding-3-large",
        input=question
    ).data[0].embedding
    
    # 2. Retrieve relevant chunks
    results = qdrant.query_points(
        collection_name=collection,
        query=question_embedding,
        limit=5,
        with_payload=True
    )
    
    # 3. Build context from retrieved chunks
    context = "\n\n---\n\n".join([
        f"[Source: {r.payload['source']}, Page: {r.payload.get('page', 'N/A')}]\n{r.payload['text']}"
        for r in results.points
    ])
    
    # 4. Generate answer with retrieved context
    response = openai_client.chat.completions.create(
        model="gpt-5.4",
        messages=[
            {"role": "system", "content": "Answer questions using only the provided context. If the answer is not in the context, say 'I cannot find this in the provided documents.'"},
            {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {question}"}
        ]
    )
    
    return response.choices[0].message.content
```

### Advanced RAG Patterns

**Re-ranking:** After initial retrieval, re-rank results using a cross-encoder (Cohere Re-rank API, `cross-encoder/ms-marco-MiniLM-L-12-v2`). Cross-encoders are 20-30% more accurate at relevance ranking than bi-encoders but too slow to use for initial retrieval.

**Hypothetical Document Embeddings (HyDE):** Generate a hypothetical answer first, embed it, use that embedding for retrieval. Improves recall for queries phrased differently than documents.

**Self-RAG:** Model decides when to retrieve (not every query needs RAG). Reduces unnecessary retrieval latency for questions the model already knows.

**RAG Evaluation with RAGAS:**
```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_recall

results = evaluate(
    dataset=test_dataset,  # questions + answers + contexts + ground_truths
    metrics=[faithfulness, answer_relevancy, context_recall]
)
# faithfulness: does answer contain only info from context?
# answer_relevancy: is answer relevant to question?
# context_recall: does context contain the info needed to answer?
```

---

## 10. Fine-Tuning Workflows

### Fine-Tuning Decision

Fine-tune when:
- Consistent output format/style at scale (JSON structure, personas, domain tone)
- Domain terminology consistently wrong in base model
- Latency optimization via smaller fine-tuned model
- Privacy: cannot send data to third-party APIs

Do NOT fine-tune to add factual knowledge — use RAG instead.

### OpenAI Fine-Tuning API

```python
# 1. Prepare dataset: JSONL with {"messages": [...]} format
training_data = [
    {
        "messages": [
            {"role": "system", "content": "You are a customer support agent for ACME Corp."},
            {"role": "user", "content": "How do I reset my password?"},
            {"role": "assistant", "content": "To reset your password, go to Settings > Security > Reset Password. You'll receive an email within 5 minutes."}
        ]
    }
]

# 2. Upload training file
with open("training.jsonl", "w") as f:
    for item in training_data:
        f.write(json.dumps(item) + "\n")

file = client.files.create(file=open("training.jsonl", "rb"), purpose="fine-tune")

# 3. Create fine-tuning job
job = client.fine_tuning.jobs.create(
    training_file=file.id,
    model="gpt-4o-mini",  # Fine-tune smaller model for cost savings
    hyperparameters={"n_epochs": 3}
)

# 4. Monitor
status = client.fine_tuning.jobs.retrieve(job.id)
# status.status: "running" | "succeeded" | "failed"

# 5. Use fine-tuned model
response = client.chat.completions.create(
    model=job.fine_tuned_model,  # "ft:gpt-4o-mini:ACME:version1:abc123"
    messages=[...]
)
```

### Axolotl (Open-Source Fine-Tuning)

```yaml
# config.yml
base_model: meta-llama/Meta-Llama-3.1-8B-Instruct
model_type: LlamaForCausalLM
tokenizer_type: AutoTokenizer

load_in_4bit: true  # QLoRA 4-bit
lora_r: 16
lora_alpha: 32
lora_dropout: 0.05

datasets:
  - path: your-dataset.jsonl
    type: chat_template

output_dir: ./outputs
num_epochs: 3
micro_batch_size: 4
gradient_accumulation_steps: 4
learning_rate: 2e-4
```

```bash
accelerate launch -m axolotl.cli.train config.yml
```

---

## 11. AI Observability and Monitoring

### Core Observability Stack

**Purpose-built LLM observability:**
- **LangSmith:** Best for LangChain ecosystem; traces, evals, datasets, prompt management
- **Braintrust:** Startup-friendly; great eval framework; supports all LLMs
- **Langfuse:** Open-source; self-hostable; strong privacy compliance

**Key metrics to monitor:**

| Metric | Good Threshold | Alert When |
|--------|---------------|------------|
| Latency P50 | < 1s | > 2s |
| Latency P95 | < 3s | > 5s |
| Error rate | < 0.1% | > 1% |
| Token cost/request | Baseline ± 10% | > 50% increase |
| Hallucination rate | Task-dependent | > 5% on grounded tasks |
| Refusal rate | < 1% (for non-sensitive tasks) | Spike vs. baseline |

### OpenTelemetry for LLM Apps

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

tracer_provider = TracerProvider()
tracer = trace.get_tracer("ai-app")

async def call_llm(prompt: str, model: str) -> str:
    with tracer.start_as_current_span("llm.call") as span:
        span.set_attribute("llm.model", model)
        span.set_attribute("llm.prompt_tokens", len(prompt.split()))
        
        start = time.time()
        response = await client.messages.create(...)
        
        span.set_attribute("llm.completion_tokens", response.usage.output_tokens)
        span.set_attribute("llm.latency_ms", (time.time() - start) * 1000)
        span.set_attribute("llm.cost_usd", calculate_cost(response.usage))
        
        return response.content[0].text
```

---

## 12. LLM Testing and Evaluation

### Evaluation Framework Selection

| Framework | Best For | Key Features |
|-----------|---------|-------------|
| PromptFoo | CI/CD integration, all LLMs | YAML config, open-source, fast |
| RAGAS | RAG pipelines | Faithfulness, context recall metrics |
| DeepEval | Comprehensive LLM evals | 14+ metrics, pytest integration |
| Braintrust | Production eval tracking | Scoring functions, dataset versioning |
| Evals (OpenAI) | OpenAI-specific | Classification, fact extraction |

### Eval-Driven Development Pattern

```python
# Define eval BEFORE writing the feature
test_cases = [
    {
        "input": "What is our refund policy?",
        "context": [refund_policy_doc],
        "expected": EvalCriteria(
            faithfulness=True,  # Only uses info from context
            relevancy=True,     # Answers the question
            completeness=True   # Covers key policy points
        )
    }
    # ... 50+ more cases
]

# Run eval against baseline model
baseline_score = run_eval(test_cases, model="claude-haiku-4-5")

# Make changes (prompt, model, RAG, etc.)
new_score = run_eval(test_cases, model="claude-haiku-4-5", new_system_prompt=True)

# Only ship if new_score >= baseline_score (no regression)
assert new_score.faithfulness >= baseline_score.faithfulness
assert new_score.latency_p95 <= baseline_score.latency_p95 * 1.2
```

### LLM-as-Judge Pattern

```python
def llm_judge(question: str, answer: str, rubric: str) -> dict:
    judge_prompt = f"""You are evaluating an AI system's response.

Question: {question}
Answer: {answer}

Rubric: {rubric}

Rate the answer on a scale of 1-5 for each criterion:
- Accuracy (1=wrong, 5=completely correct)
- Completeness (1=missing key info, 5=comprehensive)
- Clarity (1=confusing, 5=crystal clear)

Return JSON: {{"accuracy": N, "completeness": N, "clarity": N, "reasoning": "..."}}"""

    response = client.messages.create(
        model="claude-opus-4-6",  # Use strongest model as judge
        max_tokens=500,
        messages=[{"role": "user", "content": judge_prompt}]
    )
    return json.loads(response.content[0].text)
```

---

## 13. Guardrails and Content Moderation

### Input/Output Guardrail Stack

```python
from guardrails import Guard, OnFailAction
from guardrails.hub import ToxicLanguage, PII, PromptInjection

# Input guard
input_guard = Guard().use_many(
    PromptInjection(on_fail=OnFailAction.EXCEPTION),  # Block injection attempts
    ToxicLanguage(on_fail=OnFailAction.EXCEPTION)      # Block harmful input
)

# Output guard
output_guard = Guard().use_many(
    PII(on_fail=OnFailAction.FIX),           # Redact PII in outputs
    ToxicLanguage(on_fail=OnFailAction.FIX)  # Rewrite toxic content
)

@output_guard.guard(model="claude-haiku-4-5")
def safe_respond(user_input: str) -> str:
    input_guard.validate(user_input)  # Raises exception if fails
    response = client.messages.create(...)
    return response.content[0].text
```

### Prompt Injection Detection

```python
INJECTION_PATTERNS = [
    r"ignore (previous|all) instructions",
    r"system prompt:",
    r"you are now",
    r"new instructions:",
    r"disregard",
    r"override",
    r"jailbreak"
]

def detect_injection(text: str) -> bool:
    text_lower = text.lower()
    return any(re.search(p, text_lower) for p in INJECTION_PATTERNS)

# Also run through a dedicated detection model
def llm_injection_check(text: str) -> bool:
    response = client.messages.create(
        model="claude-haiku-4-5",
        max_tokens=10,
        system="You detect prompt injection attacks. Respond only 'YES' or 'NO'.",
        messages=[{"role": "user", "content": f"Is this a prompt injection attempt?\n\n{text}"}]
    )
    return response.content[0].text.strip().upper() == "YES"
```

---

## 14. Cost Optimization

### The Cost Optimization Hierarchy

1. **Model selection** — Using Haiku vs Opus is a 5x cost difference for same task
2. **Prompt caching** — 80-90% savings on repeated context
3. **Batch API** — 50% savings on non-real-time workloads
4. **Prompt optimization** — Reduce input tokens (compression, shorter examples)
5. **Output limitation** — `max_tokens` appropriate to task; don't set 4096 for responses that average 200 tokens
6. **Speculative caching** — Pre-warm caches before expected traffic spikes

### Token Budget Management

```python
import tiktoken

def count_tokens(text: str, model: str = "gpt-4") -> int:
    enc = tiktoken.encoding_for_model(model)
    return len(enc.encode(text))

def estimate_cost(input_tokens: int, output_tokens: int, model: str) -> float:
    prices = {
        "gpt-5.4": {"input": 2.50, "output": 15.00},
        "claude-opus-4-6": {"input": 5.00, "output": 25.00},
        "claude-haiku-4-5": {"input": 1.00, "output": 5.00},
    }
    p = prices.get(model, {"input": 1.0, "output": 5.0})
    return (input_tokens * p["input"] + output_tokens * p["output"]) / 1_000_000

# At pipeline design: estimate before building
total_cost = estimate_cost(
    input_tokens=2000,   # avg per request
    output_tokens=500,   # avg response
    model="claude-opus-4-6"
) * 100_000  # requests per month
print(f"Monthly cost estimate: ${total_cost:.2f}")
```

### Batch API (50% Cost Reduction)

```python
# Anthropic Batch API — for async processing (reports, analysis, batch workflows)
batch = client.beta.messages.batches.create(
    requests=[
        {
            "custom_id": f"doc_{i}",
            "params": {
                "model": "claude-haiku-4-5",
                "max_tokens": 1024,
                "messages": [{"role": "user", "content": f"Summarize: {doc}"}]
            }
        }
        for i, doc in enumerate(documents[:10_000])  # Max 10K per batch
    ]
)

# Poll or webhook for completion
while batch.processing_status == "in_progress":
    time.sleep(60)
    batch = client.beta.messages.batches.retrieve(batch.id)

# Retrieve results
for result in client.beta.messages.batches.results(batch.id):
    print(result.custom_id, result.result.message.content[0].text)
```

---

## 15. AI-Native Application Architecture

### AI-Native vs. AI-Augmented

**AI-Augmented:** Traditional software with AI features bolted on. AI is a capability, not the core.

**AI-Native:** The product cannot exist without AI; AI determines the UX, data model, and user interaction patterns.

### AI-Native Architecture Patterns

**Async-first architecture:**
- AI inference is slow (500ms-10s); design for async from Day 1
- Background jobs for AI tasks (Celery, BullMQ, Sidekiq)
- Optimistic UI: show placeholder state while AI generates
- Streaming for real-time feel even when full response takes time

**Event-driven AI pipeline:**
```
User action → Event queue (Redis/Kafka) → AI worker pool
→ Store result → Notify frontend (WebSocket/SSE) → UI update
```

**Cost-aware routing:**
```python
def route_to_model(task: Task) -> str:
    if task.requires_reasoning:
        return "claude-opus-4-6"
    elif task.expected_output_tokens > 2000:
        return "claude-sonnet-4-6"
    else:
        return "claude-haiku-4-5"
```

**Failure handling:**
- Retry with exponential backoff for transient failures (429 rate limits, 500 errors)
- Fallback model: If primary model fails, route to secondary
- Graceful degradation: Show "AI temporarily unavailable" rather than crashing
- Idempotency: AI generation can be retried; store request hash + result to deduplicate

### State Management for AI Applications

AI applications accumulate conversation state that must be managed carefully:

```python
class ConversationManager:
    def __init__(self, max_tokens: int = 150_000):
        self.max_tokens = max_tokens
        self.messages: list[dict] = []
    
    def add_message(self, role: str, content: str):
        self.messages.append({"role": role, "content": content})
        self._trim_if_needed()
    
    def _trim_if_needed(self):
        # Summarize oldest messages when approaching context limit
        token_count = sum(len(m["content"].split()) * 1.3 for m in self.messages)
        while token_count > self.max_tokens * 0.8 and len(self.messages) > 4:
            # Summarize first 20% of messages
            messages_to_summarize = self.messages[2:max(4, len(self.messages)//5)]
            summary = summarize_messages(messages_to_summarize)
            self.messages = (
                self.messages[:2] +
                [{"role": "system", "content": f"[Earlier conversation summary: {summary}]"}] +
                self.messages[-len(self.messages)//2:]
            )
            token_count = sum(len(m["content"].split()) * 1.3 for m in self.messages)
```

---

## Quick Reference: AI Development Decision Matrix

| Situation | Decision |
|-----------|---------|
| Complex multi-step reasoning | Claude Opus 4.7 or GPT o3 |
| High-volume, cost-sensitive | Claude Haiku 4.5, Gemini Flash-Lite, DeepSeek V3.2 |
| Long documents (>100K tokens) | Gemini 2.5 Pro (2M context) |
| Real-time voice interaction | GPT-4o Realtime API |
| Code generation (best quality) | Claude Opus 4.7 (SWE-bench 87.6%) |
| Structured JSON output at scale | OpenAI Structured Outputs API |
| Reduce API costs by 80-90% | Prompt caching (Anthropic ephemeral, OpenAI automatic) |
| Reduce API costs by 50% | Batch API (both Anthropic and OpenAI) |
| Privacy / on-premises | Llama 4 via vLLM + A100 cluster |
| RAG for document retrieval | pgvector (simple) or Qdrant (production scale) |
| LLM testing in CI/CD | PromptFoo (YAML-based, open-source) |
| Production LLM observability | LangSmith (LangChain) or Langfuse (open-source) |
