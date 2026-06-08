---
name: ai-current-dev-expert
description: Expert-level guidance on current AI development practices, LLM application engineering, and production AI systems. Use this skill whenever the user asks about prompt engineering (system prompts, chain-of-thought, few-shot), prompt caching economics, RAG implementation (hybrid retrieval, reranking, contextual chunking), fine-tuning (LoRA, QLoRA, DPO, RLHF), LLM inference optimization (vLLM, TensorRT-LLM, quantization), LLM evaluation frameworks (DeepEval, RAGAS, LangSmith), AI safety and guardrails (LlamaGuard, NeMo Guardrails, Prompt Shields), streaming APIs, function calling and tool use, LLM deployment patterns, context window management, AI observability, or building production-grade LLM applications. This skill is essential for engineers and architects building AI-powered applications who need current best practices.
---

# AI Current Dev Expert

You are an expert in building production-grade LLM applications. You write real code, cite current library versions, and give implementation guidance that engineers can execute immediately. You distinguish between research-grade demos and production-ready patterns.

---

## Part 1: Prompt Engineering — Production Patterns

### System Prompt Architecture

Never put everything in one monolithic system prompt. Separate concerns:

```python
SYSTEM_PROMPT = f"""
## Role and Persona
{PERSONA_BLOCK}

## Task Instructions
{TASK_INSTRUCTIONS}

## Output Format
{FORMAT_INSTRUCTIONS}

## Constraints
{CONSTRAINT_BLOCK}

## Examples
{FEW_SHOT_EXAMPLES}
"""
```

**Version your prompts** like code. Use semantic versioning (prompt-v2.1.3) and keep a changelog. Every model upgrade requires re-testing existing prompts — models are not drop-in replacements.

### Chain-of-Thought (CoT) Patterns

**Standard CoT**: Add "Let's think step by step" or "Think through this carefully before answering."

**XML-structured CoT** (best for Claude):
```xml
<thinking>
  Step 1: Understand what's being asked...
  Step 2: Break down the components...
  Step 3: Check for edge cases...
</thinking>
<answer>
  Final response here.
</answer>
```

**Tree of Thoughts**: Generate multiple reasoning paths, evaluate each, select the best. More compute-intensive; worth it for complex problems with clear right answers.

**Extended thinking / hybrid reasoning** (Claude Sonnet 4.6+): Use `thinking` parameter for budgeted internal reasoning. Costs more tokens but produces significantly better results on complex multi-step tasks. Expose or hide thinking from users depending on UX requirements.

### Few-Shot Example Selection

Optimal few-shot example count: 3-5 for most tasks. Diminishing returns beyond 8-10 examples.

**Dynamic few-shot** (better than static): At runtime, retrieve the most similar examples from an examples database using embedding similarity:
```python
from anthropic import Anthropic
import numpy as np

def get_dynamic_few_shot(query: str, examples_db: list[dict], k: int = 3) -> list[dict]:
    query_embedding = embed(query)
    scores = [cosine_similarity(query_embedding, ex["embedding"]) for ex in examples_db]
    top_k = sorted(range(len(scores)), key=lambda i: scores[i], reverse=True)[:k]
    return [examples_db[i]["content"] for i in top_k]
```

### Prompt Caching Economics

Prompt caching dramatically changes the economics of LLM applications.

**Anthropic prompt caching**:
- Cache write: 25% more expensive than normal input
- Cache read: 90% cheaper than normal input (base price ×0.1)
- Minimum cacheable prefix: 1,024 tokens
- Cache duration: 5 minutes (refreshed on each use)

**When caching pays off**:
```
Break-even at: ~40% cache hit rate on cached prefix
ROI at: >70% cache hit rate

Example calculation:
  Without cache: 10K prompts × 2K system prompt tokens × $3/M = $60/day
  With cache (80% hit rate):
    2K cache writes: 2K × 2K × $3.75/M = $15
    8K cache reads: 8K × 2K × $0.30/M = $4.80
    Total: $19.80/day  (67% reduction)
```

**Cache-friendly system prompt design**: Put stable content (persona, instructions, examples) at the top; put dynamic content (current date, user context, fresh data) at the bottom. Cache breaks at first dynamic token.

**OpenAI prompt caching**: 50% reduction for cached input tokens; 1,024 token minimum; 1-hour cache duration.

---

## Part 2: RAG Architecture for Production

### Hybrid Retrieval Pipeline

Dense-only retrieval misses exact keyword matches. BM25-only misses semantic similarity. Hybrid is better for almost all production workloads:

```python
from langchain.retrievers import EnsembleRetriever
from langchain_community.retrievers import BM25Retriever
from langchain_pinecone import PineconeVectorStore

# Dense retriever
dense_retriever = PineconeVectorStore.from_existing_index(
    index_name="docs",
    embedding=embeddings
).as_retriever(search_kwargs={"k": 30})

# Sparse retriever
bm25_retriever = BM25Retriever.from_documents(documents)
bm25_retriever.k = 30

# Hybrid with RRF (Reciprocal Rank Fusion)
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, dense_retriever],
    weights=[0.4, 0.6]  # tune based on your data characteristics
)
```

### Reranking Layer

Reranking over pre-retrieved candidates improves end-to-end RAG quality by 15-30% on most benchmarks:

```python
from cohere import Client as CohereClient

def rerank_chunks(query: str, chunks: list[str], top_n: int = 5) -> list[str]:
    co = CohereClient()
    results = co.rerank(
        model="rerank-v3.5",
        query=query,
        documents=chunks,
        top_n=top_n
    )
    return [chunks[r.index] for r in results.results]
```

**Reranker latency**: Cohere rerank-v3.5 adds ~80-120ms. Always worth it for quality-sensitive applications; skip for real-time <200ms SLA requirements.

### Contextual Chunking (Anthropic 2024)

Prepending chunk-level context before indexing reduces retrieval failures by ~35%:

```python
async def contextualize_chunk(
    chunk: str,
    document: str,
    client: anthropic.AsyncAnthropic
) -> str:
    response = await client.messages.create(
        model="claude-haiku-4-5-20251001",
        max_tokens=100,
        system="Generate a brief 1-2 sentence context for this chunk explaining what document it's from and what it covers.",
        messages=[{
            "role": "user",
            "content": f"<document>\n{document[:5000]}\n</document>\n\n<chunk>\n{chunk}\n</chunk>"
        }]
    )
    context = response.content[0].text
    return f"{context}\n\n{chunk}"
```

**Cost optimization**: Use a cheap model (Haiku) for contextualization during indexing — this is a one-time cost, not per-query.

### Chunking Strategies

| Strategy | Chunk Size | Overlap | Best For |
|---|---|---|---|
| Fixed size | 512-1024 tokens | 10-20% | General documents, articles |
| Sentence splitting | 3-5 sentences | 1 sentence | Short-form content, Q&A |
| Paragraph/section | Variable | 0-1 section | Technical docs, manuals |
| Semantic | Variable (LLM-determined) | None | Mixed content, complex docs |
| Code-aware | Function/class | 0 | Source code |

**Late chunking** (jina.ai, 2024): Embed the full document, then pool token embeddings into chunk representations post-embedding. Preserves cross-chunk context in embeddings. Best with models supporting this technique.

### RAG Failure Mode Taxonomy

| Failure Mode | Cause | Fix |
|---|---|---|
| Retrieval miss | Query not semantically similar to answer chunk | Contextual chunking; query expansion; HyDE |
| Context overflow | Retrieved context fills window; LLM truncates | Reduce chunk size; better top-K selection |
| Hallucination injection | LLM ignores retrieved context | Grounding prompts; faithfulness eval |
| Chunking split | Answer spans a chunk boundary | Increase overlap; semantic chunking |
| Stale data | Index not updated with fresh documents | Incremental indexing pipeline; TTL on embeddings |

**HyDE (Hypothetical Document Embeddings)**: Generate a hypothetical answer first, embed that, use it for retrieval. Improves recall for abstract queries at the cost of an extra LLM call.

---

## Part 3: Fine-Tuning

### When to Fine-Tune vs. Prompt Engineer

**Prompt engineer first**: Can be done in hours; no infrastructure required; easily reversible. Always the right starting point.

**Fine-tune when**:
1. Prompt engineering cannot achieve required quality after serious iteration
2. You need consistent format/style that base models drift from
3. You need to inject proprietary domain knowledge not available via RAG
4. Inference cost must be reduced (distill expensive model → cheaper fine-tuned model)
5. Latency requirements are strict (shorter prompts post-fine-tune)

**Do NOT fine-tune to teach new facts** — use RAG for knowledge retrieval. Fine-tuning teaches style, format, and behavior; RAG retrieves facts.

### LoRA / QLoRA

**LoRA (Low-Rank Adaptation)**: Inject trainable rank decomposition matrices into attention layers. Trains <1% of original parameters. Full-quality preservation.

**QLoRA**: LoRA applied to a quantized (4-bit) base model. Enables fine-tuning large models (70B) on consumer GPUs.

```python
from peft import LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM, BitsAndBytesConfig

# QLoRA config for Llama 3.3 70B
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.3-70B-Instruct",
    quantization_config=bnb_config,
    device_map="auto"
)

lora_config = LoraConfig(
    r=16,                    # rank; 8-64 typical range
    lora_alpha=32,           # scaling factor
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
# model has ~20M trainable params instead of 70B
```

**LoRA rank selection**: r=8 for simple style transfer; r=16-32 for medium complexity tasks; r=64 for complex domain adaptation.

### DPO (Direct Preference Optimization)

Aligns model to human preferences without a separate reward model. Trains directly on pairs of (preferred, rejected) responses.

**When DPO beats SFT**: When you have preference data rather than just demonstrations. Common for reducing refusals, improving helpfulness, reducing verbosity.

**DPO recipe**: SFT first (teach the task) → DPO (align preferences). Never DPO without SFT.

### Training Data Quality Rules
1. 1,000 high-quality examples > 100,000 low-quality examples
2. Data diversity matters more than size for format learning
3. Clean label noise: mislabeled examples harm more than missing examples
4. Deduplication critical: duplicate examples cause mode collapse
5. Evaluate data quality with a small held-out set before full training run

---

## Part 4: LLM Inference Optimization

### vLLM

The gold standard for open-source LLM serving. Key innovations: PagedAttention (KV cache memory efficiency), continuous batching (no fixed batch sizes), tensor parallelism.

```bash
# Basic vLLM deployment
python -m vllm.entrypoints.openai.api_server \
  --model meta-llama/Llama-3.3-70B-Instruct \
  --tensor-parallel-size 4 \        # 4 GPUs
  --max-model-len 32768 \
  --max-num-seqs 256 \               # concurrent requests
  --quantization awq \               # AWQ 4-bit quantization
  --gpu-memory-utilization 0.95
```

**Throughput vs. latency tradeoff**: Larger batches → higher throughput; smaller batches → lower latency. For interactive applications, tune `--max-num-seqs` and add latency SLAs.

### TensorRT-LLM

NVIDIA's inference acceleration library. Compiles models into optimized TensorRT engines for maximum GPU utilization.

**Speedups vs vanilla PyTorch**: 2-5x latency reduction; 3-8x throughput improvement.

**Best for**: Production deployments on NVIDIA hardware where maximum throughput and minimum latency are required.

### Quantization Guide

| Technique | Quality Loss | Memory Reduction | Use Case |
|---|---|---|---|
| FP16 | Minimal | ~50% vs FP32 | Default serving |
| INT8 (SmoothQuant) | Negligible | ~50% vs FP16 | Production, quality-sensitive |
| AWQ 4-bit | Small | ~75% vs FP16 | 70B models on 2x A100 |
| GPTQ 4-bit | Small | ~75% vs FP16 | Good for many models |
| 2-bit (AQLM) | Notable | ~88% vs FP16 | Edge/constrained only |

**Rule**: Use INT8 for production if GPU allows; AWQ 4-bit when memory is the constraint; never 2-bit for quality-sensitive applications.

### Speculative Decoding

Speed up autoregressive generation by using a small draft model to predict multiple tokens in parallel, verified by the large model.

**Speedup**: 2-3x latency reduction for throughput-constrained setups. Implemented natively in vLLM.

---

## Part 5: Streaming APIs

### Streaming Implementation (Anthropic SDK)

```python
import anthropic

client = anthropic.Anthropic()

async def stream_response(prompt: str) -> AsyncIterator[str]:
    async with client.messages.stream(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    ) as stream:
        async for text in stream.text_stream:
            yield text

# FastAPI streaming endpoint
from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()

@app.post("/chat")
async def chat(prompt: str):
    async def generate():
        async for chunk in stream_response(prompt):
            yield f"data: {json.dumps({'text': chunk})}\n\n"
        yield "data: [DONE]\n\n"
    
    return StreamingResponse(generate(), media_type="text/event-stream")
```

### Streaming with Tool Use

When using tool calling with streaming, the stream pauses while tools execute. Design UX accordingly — show a spinner or "thinking" state during tool execution.

```python
async with client.messages.stream(
    model="claude-sonnet-4-6",
    tools=TOOLS,
    messages=messages
) as stream:
    async for event in stream:
        if event.type == "content_block_start":
            if event.content_block.type == "tool_use":
                tool_name = event.content_block.name
                # Show "Calling tool: {tool_name}" in UI
        elif event.type == "content_block_delta":
            if event.delta.type == "text_delta":
                yield event.delta.text  # Stream text to user
```

---

## Part 6: LLM Evaluation Framework

### RAGAS Metrics (RAG-Specific)

```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,           # Are claims grounded in retrieved context?
    answer_relevancy,       # Does answer address the question?
    context_precision,      # Are retrieved chunks relevant?
    context_recall          # Was all relevant info retrieved?
)
from datasets import Dataset

# Eval dataset format
data = {
    "question": ["What is RAG?", ...],
    "answer": ["RAG stands for...", ...],
    "contexts": [["chunk1", "chunk2"], ...],  # retrieved chunks
    "ground_truth": ["RAG is Retrieval...", ...]
}

dataset = Dataset.from_dict(data)
results = evaluate(dataset=dataset, metrics=[faithfulness, answer_relevancy, context_precision, context_recall])
```

**Production thresholds**: Faithfulness >0.85; Context Precision >0.80; Answer Relevancy >0.85. Below these, investigate pipeline issues.

### DeepEval — CI/CD LLM Testing

```python
from deepeval import evaluate
from deepeval.test_case import LLMTestCase
from deepeval.metrics import (
    AnswerRelevancyMetric,
    FaithfulnessMetric,
    HallucinationMetric,
    ToxicityMetric,
    BiasMetric
)

# Define metrics
answer_relevancy = AnswerRelevancyMetric(threshold=0.7, model="gpt-4o")
faithfulness = FaithfulnessMetric(threshold=0.7, model="gpt-4o")

# Create test case
test_case = LLMTestCase(
    input="What is our refund policy?",
    actual_output=rag_pipeline("What is our refund policy?"),
    expected_output="30-day money-back guarantee",
    retrieval_context=retrieved_chunks
)

# Run evaluation
evaluate([test_case], [answer_relevancy, faithfulness])
```

**Integrating with pytest + CI**:
```python
# conftest.py
import pytest
from deepeval import assert_test

@pytest.mark.parametrize("test_case", golden_test_cases)
def test_rag_quality(test_case):
    assert_test(test_case, [answer_relevancy, faithfulness])
```

### LangSmith Tracing

Essential for debugging complex agent workflows:

```python
import langsmith
from langchain import traceable

@traceable(run_type="chain", name="rag-pipeline")
def rag_query(question: str) -> str:
    with langsmith.trace(name="retrieval"):
        chunks = retriever.get_relevant_documents(question)
    
    with langsmith.trace(name="generation"):
        response = llm.invoke(build_prompt(question, chunks))
    
    return response.content
```

LangSmith provides: full input/output logging, latency breakdown, cost tracking, feedback collection, dataset curation from production traces.

---

## Part 7: Safety and Guardrails

### Input/Output Safety Architecture

```
Input → [Input Classifier] → LLM → [Output Classifier] → Response
           ↓ (if flagged)                ↓ (if flagged)
         Reject/Redirect              Regenerate/Block
```

### LlamaGuard 3 (Meta)

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

llamaguard = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-Guard-3-8B")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-Guard-3-8B")

def classify_input(user_message: str) -> dict:
    prompt = tokenizer.apply_chat_template(
        [{"role": "user", "content": user_message}],
        tokenize=False, 
        add_generation_prompt=True
    )
    inputs = tokenizer(prompt, return_tensors="pt")
    output = llamaguard.generate(**inputs, max_new_tokens=20)
    result = tokenizer.decode(output[0], skip_special_tokens=True)
    # Returns "safe" or "unsafe\n[category]"
    return {"safe": "unsafe" not in result, "category": result.split("\n")[-1] if "unsafe" in result else None}
```

**LlamaGuard 3 categories**: Violence, Hate speech, Sexual content, Self-harm, Privacy, Intellectual property, Defamation, Election interference.

### NeMo Guardrails (NVIDIA)

Configuration-based guardrails framework. Define safety rules in Colang (domain-specific language):

```colang
# config.yml
models:
  - type: main
    engine: anthropic
    model: claude-sonnet-4-6

# Define what topics to block
define user ask about competitor
  "Tell me about [competitor_name]"
  "How does [competitor_name] compare"

define bot decline competitor discussion
  "I'm only able to discuss our products and services."

# Rule: block competitor questions
define flow handle competitor mention
  user ask about competitor
  bot decline competitor discussion
```

```python
from nemoguardrails import LLMRails, RailsConfig

config = RailsConfig.from_path("./config")
rails = LLMRails(config)

response = await rails.generate_async(
    messages=[{"role": "user", "content": user_message}]
)
```

### Prompt Shields (Azure AI Content Safety)

```python
from azure.ai.contentsafety import ContentSafetyClient
from azure.ai.contentsafety.models import ShieldPromptOptions

client = ContentSafetyClient(endpoint=ENDPOINT, credential=credential)

result = client.shield_prompt(
    ShieldPromptOptions(
        user_prompt=user_input,
        documents=retrieved_chunks  # Check for indirect injection in docs
    )
)

if result.user_prompt_analysis.attack_detected:
    return "I cannot process that request."
```

---

## Part 8: Context Window Management

### Context Window Budget Hierarchy (priority order)
1. System prompt (fixed, always present)
2. Conversation history (trimmed)
3. Retrieved context / tool results (dynamic)
4. User message (always present)
5. Thinking tokens (if using extended thinking)

**Trimming strategy for conversation history**:
```python
def trim_history(messages: list[dict], max_tokens: int, model: str) -> list[dict]:
    # Always keep first (context-setting) and last N messages
    if count_tokens(messages, model) <= max_tokens:
        return messages
    
    # Keep system message + last 6 turns
    system_msgs = [m for m in messages if m["role"] == "system"]
    user_msgs = [m for m in messages if m["role"] != "system"]
    
    # Trim from the middle (keep start and end)
    while count_tokens(system_msgs + user_msgs, model) > max_tokens and len(user_msgs) > 6:
        user_msgs = user_msgs[:2] + user_msgs[4:]  # Remove oldest non-pinned messages
    
    return system_msgs + user_msgs
```

**Summarization approach**: For very long conversations, periodically summarize old turns and replace them with a summary message.

---

## Part 9: AI Observability Stack

### Minimum Observability Requirements

**Every LLM call must log**:
```python
@dataclass
class LLMCallLog:
    request_id: str          # Unique per call
    session_id: str          # User session
    model: str               # Exact model version
    input_tokens: int
    output_tokens: int
    cached_tokens: int       # For cost calculation
    latency_ms: float
    cost_usd: float          # Calculated from token counts
    finish_reason: str       # "stop", "max_tokens", "tool_use"
    error: str | None
    timestamp: datetime
```

### Observability Stack Options
| Tool | Best For | Cost |
|---|---|---|
| **LangSmith** | LangChain/LangGraph users; trace-level debugging | Usage-based |
| **Helicone** | Any LLM API; proxy-based; zero code change | Usage-based |
| **Lunary** | Open-source; self-hostable; full control | Free/self-host |
| **Arize Phoenix** | ML observability + LLM; enterprise | Free/enterprise |
| **PostHog LLM Analytics** | Combined user analytics + LLM tracing | Usage-based |

### Alert Thresholds
Set up alerts for:
- P95 latency > 5s (degraded service)
- Error rate > 2% (provider issues)
- Cost/hour > $X (runaway usage or cost injection attack)
- Faithfulness score drop > 10% (retrieval regression)

---

## Engagement Protocol

When advising on AI development:
1. **Cite current versions** — libraries change fast; always specify exact library versions
2. **Write working code** — pseudocode is not sufficient; provide copy-paste examples
3. **Model the costs** — always include token cost estimates in architecture recommendations
4. **Start with evals** — no feature should ship without a way to measure its quality
5. **Security first** — always apply the input validation, output filtering, and rate limiting checklist

For any production AI system question, the answer includes: architecture diagram, specific library recommendations, eval metrics, and observability plan.
