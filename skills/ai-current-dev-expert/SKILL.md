---
name: AI Current Dev Expert
description: Expert-level knowledge of the current AI development landscape (2025–2026), covering frontier model capabilities, inference infrastructure, fine-tuning techniques, RAG systems, AI deployment patterns, evaluation, and emerging tools. Practical guidance for engineers building production AI systems.
---

# AI Current Dev Expert

You are an expert-level practitioner in current AI development. You know the state of frontier models, inference infrastructure, fine-tuning techniques, RAG architectures, AI evaluation, and the tools used by top AI engineering teams in 2025–2026. You avoid outdated patterns (BERT is not "current", Python 3.8 is not "modern"), give concrete implementation guidance, and distinguish between what works in research vs. what works in production. Your knowledge reflects the genuine state of the field, not hype.

---

## 1. Frontier Model Landscape (2025–2026)

### Closed-Source Frontier Models

| Model | Context | Input Cost/1M | Output Cost/1M | Best For |
|---|---|---|---|---|
| Claude Opus 4.8 | 200K | $15 | $75 | Complex reasoning, analysis |
| Claude Sonnet 4.6 | 200K | $3 | $15 | Balanced daily use, coding |
| Claude Haiku 4.5 | 200K | $0.80 | $4 | High-volume, speed-critical |
| GPT-4.1 | 1M | $2 | $8 | Code, multimodal, long-context |
| GPT-o3 | 200K | $10 | $40 | Math, science, multi-step reasoning |
| GPT-o4-mini | 200K | $1.10 | $4.40 | Efficient reasoning |
| Gemini 2.5 Pro | 1M | $1.25 | $10 | Long-context, multimodal |
| Gemini 2.5 Flash | 1M | $0.15 | $0.60 | Speed + cost |

*Pricing approximate as of mid-2026; verify current pricing before production decisions*

### Open-Source / Locally Deployable Models

| Model | Params | Context | License | Notable |
|---|---|---|---|---|
| Llama 4 Scout | 109B active (MoE, 16 experts) | 10M | Llama 4 Community | Ultra-long context |
| Llama 4 Maverick | 400B active (MoE, 128 experts) | 1M | Llama 4 Community | Near-frontier reasoning |
| DeepSeek R1 | 671B active (MoE) | 128K | MIT | Math/coding, open weights |
| DeepSeek V3-0324 | 671B active (MoE) | 128K | MIT | General capability |
| Qwen 2.5 72B Instruct | 72B | 128K | Apache 2.0 | Multilingual, strong code |
| Phi-4 | 14B | 16K | MIT | Efficient, small model |
| Gemma 3 27B | 27B | 128K | Gemma ToS | Google's open model |
| Mistral Small 3 | 24B | 128K | Apache 2.0 | European, efficient |

### Model Selection Decision Tree

```
Need >200K context?
├── Yes → Gemini 2.5 Pro (1M) or Llama 4 Scout (10M)
└── No
    └── Primary use: Code?
        ├── Yes → GPT-4.1 or Claude Sonnet 4.6
        └── No
            └── Multi-step reasoning (math/science)?
                ├── Yes → GPT-o3 or Claude Opus 4.8
                └── No
                    └── Cost-sensitive / high volume?
                        ├── Yes → Gemini 2.5 Flash or Claude Haiku 4.5
                        └── No → Claude Sonnet 4.6 (default)
```

### Open Source vs. Closed Source Decision

**Choose closed-source when**:
- Data privacy requirements are manageable via DPA/SOC2
- Need best-in-class capability without ops overhead
- Rapid prototyping, unknown volume

**Choose open-source when**:
- Data must not leave your infrastructure (PII, PHI, ITAR)
- Cost at scale favors hosting (typically >10M tokens/day)
- Need fine-tuning on proprietary data
- Regulatory requirement for model explainability / auditability
- EU AI Act high-risk category requiring transparency

---

## 2. Inference Infrastructure

### Inference Stack Evolution (2022–2026)

```
2022: HuggingFace Transformers (CPU/GPU, research-grade)
2023: TGI (Text Generation Inference) — first production-ready
2024: vLLM dominance — PagedAttention, continuous batching
2025: vLLM + SGLang + TensorRT-LLM for production
2026: OpenAI-compatible API as universal interface standard
```

### vLLM (Primary Open-Source Inference Engine)

**Why vLLM is dominant**:
- PagedAttention: Virtual memory for KV cache → 2–4x throughput vs. naïve batching
- Continuous batching: Requests don't wait for longest sequence → lower latency
- OpenAI-compatible API: Drop-in replacement for most applications
- Multi-backend: CUDA, ROCm, CPU, TPU
- Speculative decoding: Draft model generates tokens, target model verifies → lower latency

```bash
# Launch vLLM server (OpenAI-compatible)
pip install vllm
python -m vllm.entrypoints.openai.api_server \
  --model meta-llama/Llama-4-Scout-17B-16E-Instruct \
  --tensor-parallel-size 4 \
  --max-model-len 32768 \
  --enable-prefix-caching \
  --gpu-memory-utilization 0.9
```

```python
# Client: same as OpenAI SDK
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8000/v1", api_key="EMPTY")
response = client.chat.completions.create(
    model="meta-llama/Llama-4-Scout-17B-16E-Instruct",
    messages=[{"role": "user", "content": "Explain attention mechanisms"}],
    max_tokens=1000,
)
```

### SGLang (Structured Generation)

SGLang (2024) excels over vLLM for:
- Complex multi-turn structured generation
- Constrained decoding (JSON/regex output)
- RadixAttention for KV cache sharing across requests
- ~4x higher throughput than vLLM for certain workloads

```python
import sglang as sgl

@sgl.function
def multi_turn_chat(s, question1, question2):
    s += sgl.system("You are a helpful assistant.")
    s += sgl.user(question1)
    s += sgl.assistant(sgl.gen("answer1", max_tokens=200))
    s += sgl.user(question2)
    s += sgl.assistant(sgl.gen("answer2", max_tokens=200))

state = multi_turn_chat.run(
    question1="What is attention?",
    question2="How does self-attention differ from cross-attention?",
    backend=sgl.RuntimeEndpoint("http://localhost:30000")
)
```

### Quantization: Production Options

| Method | Quality Loss | Speed Gain | Memory Reduction | Use Case |
|---|---|---|---|---|
| **FP16** | None | Baseline | Baseline | Default for A100/H100 |
| **BF16** | ~None | ~5% faster | None vs FP16 | Default for modern GPUs |
| **INT8 (LLM.int8)** | ~1% | 1.5x | ~2x | CPU offload scenarios |
| **GPTQ INT4** | ~2-3% | 2-3x | ~4x | Inference-only, batch |
| **AWQ INT4** | ~1-2% | 2-3x | ~4x | Better calibration than GPTQ |
| **GGUF Q4_K_M** | ~2-3% | 3-4x | ~4x | CPU/consumer GPU (llama.cpp) |
| **FP8** | <1% | 1.5-2x | ~2x | H100+ (hardware native) |

```python
# AWQ quantized loading (best INT4 quality)
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "Qwen/Qwen2.5-72B-Instruct-AWQ",
    device_map="auto",
    torch_dtype="auto",
)
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-72B-Instruct-AWQ")
```

### Hardware Guide (2026)

| GPU | VRAM | FP16 TFLOPs | Best For |
|---|---|---|---|
| H100 SXM | 80GB | 989 | Large models, training |
| H100 PCIe | 80GB | 756 | Inference at scale |
| A100 80GB | 80GB | 312 | Training, proven stack |
| H200 | 141GB | 989 | Very large models (70B+) |
| RTX 4090 | 24GB | 82 | Consumer dev, 13B-34B |
| RTX 5090 | 32GB | 166 | Consumer dev, up to 70B Q4 |

**Tensor parallelism guide**: 7B → 1 GPU; 13B → 1 GPU (A100) or 2×RTX4090; 70B → 2×A100 (FP16); 405B → 8×A100 (FP16)

---

## 3. Fine-Tuning Techniques

### Fine-Tuning Hierarchy (Cost vs. Performance)

```
Most Expensive / Best Performance:
  Full fine-tuning (all params updated)
  ↓
  LoRA / QLoRA (low-rank adapters)
  ↓
  Prefix tuning / P-tuning
  ↓
  Prompt engineering + few-shot
Least Expensive / Sufficient for Most Use Cases
```

### LoRA / QLoRA (Standard for 2025–2026)

**LoRA** (Low-Rank Adaptation): Freezes base model, trains low-rank decomposition matrices. Only adds ~0.1% of params while capturing ~90% of full fine-tune quality.

**QLoRA**: Load base model in 4-bit, train LoRA adapters in 16-bit. Enables 70B fine-tuning on 2×A100.

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
from peft import LoraConfig, get_peft_model, TaskType
import torch

# QLoRA: 4-bit base + 16-bit LoRA
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16,
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-4-Scout-17B-16E-Instruct",
    quantization_config=bnb_config,
    device_map="auto",
)

lora_config = LoraConfig(
    r=16,           # Rank: 8-32 typical; higher = more params = better quality
    lora_alpha=32,  # Scaling: typically 2x rank
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type=TaskType.CAUSAL_LM,
)

model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# trainable params: 41,943,040 || all params: 7,241,748,480 || trainable%: 0.5794%
```

### Unsloth (2024–2026 Standard for Efficient Fine-Tuning)

Unsloth: 2x faster training, 60% less VRAM than HuggingFace + PEFT alone.

```python
from unsloth import FastLanguageModel
import torch

model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="meta-llama/Llama-4-Scout-17B-16E-Instruct",
    max_seq_length=4096,
    dtype=torch.bfloat16,
    load_in_4bit=True,
)

model = FastLanguageModel.get_peft_model(
    model,
    r=16,
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj",
                    "gate_proj", "up_proj", "down_proj"],
    lora_alpha=16,
    lora_dropout=0,  # Optimized: 0 dropout for speed
    bias="none",
    use_gradient_checkpointing="unsloth",
)
```

### Alignment Techniques (Post-Training)

| Technique | What It Does | When to Use |
|---|---|---|
| **SFT** (Supervised Fine-Tuning) | Train on input/output pairs | Any task-specific fine-tuning |
| **RLHF** | Reward model + PPO from human preferences | Safety, helpfulness alignment |
| **DPO** (Direct Preference Optimization) | Simplified RLHF without RL | Preference data, simpler than RLHF |
| **ORPO** (Odds Ratio Preference) | SFT + alignment in one stage | Efficiency when DPO is overkill |
| **KTO** (Kahneman-Tversky) | Binary feedback (good/bad) | When you have thumbs up/down, not pairs |

**2025 recommendation**: DPO is the new default for preference alignment. RLHF only if you need nuanced reward shaping.

### Dataset Curation for Fine-Tuning

Quality > quantity: 10K high-quality examples often outperforms 1M noisy ones.

```python
# Dataset format for instruction fine-tuning
{
    "messages": [
        {"role": "system", "content": "You are a legal document reviewer..."},
        {"role": "user", "content": "Summarize this contract clause: [CLAUSE]"},
        {"role": "assistant", "content": "This clause establishes..."}
    ]
}
```

**Data quality checklist**:
- No harmful or biased content
- Consistent formatting (JSON/conversation structure)
- Representative of production distribution
- Deduplicated (MinHash LSH deduplication)
- Length-appropriate responses (not all one-liners, not all essays)

---

## 4. RAG (Retrieval-Augmented Generation)

### RAG Architecture Evolution

```
Naive RAG (2023): Query → Embed → Top-k chunks → LLM
Basic RAG (2024): + reranking, chunking strategies
Advanced RAG (2025): + hybrid search, HyDE, multi-hop
Agentic RAG (2025-2026): + dynamic retrieval, self-correction, tool use
```

### Hybrid Search (2025 Standard)

Combine dense (semantic) + sparse (keyword) retrieval:

```python
from qdrant_client import QdrantClient
from qdrant_client.models import (
    VectorParams, Distance, SparseVectorParams,
    SparseIndexParams, NamedVector, NamedSparseVector,
    SearchRequest, Query
)

# Hybrid search: dense + BM25 sparse
client = QdrantClient(url="http://localhost:6333")

# Dense search with sparse reranking (RRF fusion)
results = client.query_points(
    collection_name="documents",
    query=Query(
        fuse=FusionQuery(fusion=Fusion.RRF),  # Reciprocal Rank Fusion
    ),
    prefetch=[
        # Dense semantic search
        PrefetchQuery(
            query=dense_embedding,
            using="dense",
            limit=20,
        ),
        # Sparse BM25 keyword search
        PrefetchQuery(
            query=SparseVector(indices=sparse_indices, values=sparse_values),
            using="sparse",
            limit=20,
        ),
    ],
    limit=10,
)
```

### Chunking Strategies

| Strategy | Chunk Size | Best For | Pitfall |
|---|---|---|---|
| Fixed-size | 512 tokens | Simple retrieval | Cuts mid-sentence |
| Sentence | Variable | Natural boundaries | Short chunks lose context |
| Semantic | Variable | Topic coherence | Expensive to compute |
| **Hierarchical** | Parent + child | **Best for production** | Complex to implement |
| Late chunking | Full doc → chunk embeddings | Long docs | Newer, less tooling |

**Hierarchical chunking (2025 best practice)**:
```python
# Parent document retriever pattern
# Retrieve by child chunk, return parent context
from langchain.retrievers import ParentDocumentRetriever
from langchain.storage import InMemoryStore
from langchain_text_splitters import RecursiveCharacterTextSplitter

parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)

retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=InMemoryStore(),
    child_splitter=child_splitter,
    parent_splitter=parent_splitter,
)
```

### Reranking (Critical for Quality)

Adding a reranker is the single highest-ROI improvement to naive RAG:
- Retrieval recalls top-50, reranker selects top-5
- Quality improvement: typically +15–25% on relevance metrics

```python
# Cohere Rerank 3.5 (2025 best reranker)
import cohere
co = cohere.Client("API_KEY")

results = co.rerank(
    model="rerank-v3.5",
    query=user_query,
    documents=[doc.page_content for doc in retrieved_docs],
    top_n=5,
    return_documents=True,
)

reranked_docs = [result.document for result in results.results]
```

**Open-source rerankers**: BGE-Reranker-v2-M3 (BAAI), ms-marco-MiniLM-L-12-v2 (cross-encoder)

### HyDE (Hypothetical Document Embeddings)

For queries where the answer is stylistically different from the question:
```python
# Generate hypothetical answer, embed that instead of (or in addition to) the question
def hyde_retrieve(query: str, retriever, llm) -> list:
    hypothetical_answer = llm.invoke(
        f"Write a 1-paragraph answer to: {query}"
    )
    # Embed the hypothetical answer
    hyde_embedding = embed(hypothetical_answer)
    # Retrieve using both original query and HyDE
    original_results = retriever.invoke(query)
    hyde_results = vectorstore.similarity_search_by_vector(hyde_embedding, k=10)
    return deduplicate(original_results + hyde_results)
```

### Embedding Models (2025–2026)

| Model | Dims | Context | License | MTEB Score |
|---|---|---|---|---|
| text-embedding-3-large | 3072 | 8191 | Proprietary | 64.6 |
| text-embedding-3-small | 1536 | 8191 | Proprietary | 62.3 |
| voyage-3-large | 2048 | 32000 | Proprietary | 68.0+ |
| E5-Mistral-7B | 4096 | 32768 | MIT | 66.6 |
| GTE-Qwen2-7B | 3584 | 32768 | Apache 2.0 | 72.1 |
| BGE-M3 | 1024 | 8192 | MIT | 66.5 |

**2025 recommendation**: voyage-3-large for maximum quality; GTE-Qwen2-7B for open-source; text-embedding-3-small for OpenAI integration.

---

## 5. DSPy: Systematic Prompt Engineering

### What DSPy Solves

Manual prompt engineering is:
- Not reproducible
- Not maintainable at scale
- Not optimizable

DSPy (2023–2025, Stanford) treats prompts as hyperparameters to optimize, not strings to hand-craft.

```python
import dspy

# Define program structure with signatures
class RAGSignature(dspy.Signature):
    """Answer questions based on retrieved context."""
    context: list[str] = dspy.InputField(desc="Retrieved document chunks")
    question: str = dspy.InputField()
    answer: str = dspy.OutputField(desc="Detailed answer based on context")

class RAGProgram(dspy.Module):
    def __init__(self):
        self.retrieve = dspy.Retrieve(k=5)
        self.generate = dspy.ChainOfThought(RAGSignature)

    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate(context=context, question=question)

# Configure LM
dspy.configure(lm=dspy.LM("anthropic/claude-sonnet-4-6"))

# Compile: automatically optimize prompts on training examples
from dspy.teleprompt import BootstrapFewShotWithRandomSearch

teleprompter = BootstrapFewShotWithRandomSearch(
    metric=your_eval_metric,
    max_bootstrapped_demos=8,
    num_candidate_programs=10,
)
optimized_program = teleprompter.compile(RAGProgram(), trainset=train_examples)
```

---

## 6. Structured Output / Constrained Generation

### Why Structured Output Matters
LLM output variability breaks pipelines. The solution:

1. **Provider-native JSON mode** (OpenAI, Anthropic, Gemini all support)
2. **Pydantic + instructor library** (most robust)
3. **Outlines / SGLang** (grammar-constrained decoding, works locally)

```python
# Instructor: best DX for structured extraction
import instructor
from anthropic import Anthropic
from pydantic import BaseModel, Field

class ContractRisk(BaseModel):
    risk_level: Literal["low", "medium", "high", "critical"]
    risk_factors: list[str] = Field(min_items=1)
    key_clauses: list[str]
    recommendation: str

client = instructor.from_anthropic(Anthropic())

result = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": f"Analyze this contract: {contract_text}"
    }],
    response_model=ContractRisk,
)
# result is a typed ContractRisk object, not a string
print(result.risk_level)  # "high"
```

### Outlines (Grammar-Constrained, Local Models)

```python
import outlines

model = outlines.models.transformers("Qwen/Qwen2.5-7B-Instruct")

# Guarantee JSON output matching schema
generator = outlines.generate.json(model, ContractRisk)
result = generator("Analyze this contract: " + contract_text)
```

---

## 7. Agentic AI Patterns

### Tool Use / Function Calling

```python
# Anthropic tool use (2025 pattern)
import anthropic
import json

tools = [
    {
        "name": "search_web",
        "description": "Search the web for current information",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "Search query"},
                "max_results": {"type": "integer", "default": 5}
            },
            "required": ["query"]
        }
    }
]

client = anthropic.Anthropic()
messages = [{"role": "user", "content": "What's the current price of NVIDIA?"}]

# Agentic loop
while True:
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=4096,
        tools=tools,
        messages=messages,
    )

    if response.stop_reason == "end_turn":
        break

    if response.stop_reason == "tool_use":
        tool_use_block = next(b for b in response.content if b.type == "tool_use")
        tool_result = execute_tool(tool_use_block.name, tool_use_block.input)

        messages.append({"role": "assistant", "content": response.content})
        messages.append({
            "role": "user",
            "content": [{
                "type": "tool_result",
                "tool_use_id": tool_use_block.id,
                "content": json.dumps(tool_result)
            }]
        })
```

### Vercel AI SDK v5 (2025 Standard for Web Apps)

```typescript
import { streamText } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';

// Server action or API route
export async function POST(request: Request) {
  const { messages } = await request.json();

  const result = streamText({
    model: anthropic('claude-sonnet-4-6'),
    messages,
    tools: {
      searchWeb: {
        description: 'Search the web',
        parameters: z.object({ query: z.string() }),
        execute: async ({ query }) => fetchSearchResults(query),
      },
    },
    maxSteps: 5, // Allow multi-step tool use
  });

  return result.toDataStreamResponse();
}
```

---

## 8. LLM Observability and Evaluation

### LLM Operations (LLMOps) Stack 2025

| Layer | Tools |
|---|---|
| **Tracing** | LangSmith, Langfuse (open-source), Phoenix (Arize) |
| **Evaluation** | RAGAS, deepeval, inspect-ai, Braintrust |
| **Monitoring** | Langfuse, PromptLayer, Helicone |
| **Prompt Management** | LangSmith Hub, Langfuse Prompts |
| **Testing** | pytest-llm, PromptFoo |

### RAGAS: RAG Evaluation Suite

```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,           # Does answer stick to context? (no hallucination)
    answer_relevancy,       # Does answer address the question?
    context_recall,         # Did retrieval find all relevant info?
    context_precision,      # Is retrieved context relevant?
)

from datasets import Dataset

data = {
    "question": questions,
    "answer": generated_answers,
    "contexts": retrieved_contexts,
    "ground_truth": reference_answers,  # optional
}

dataset = Dataset.from_dict(data)
result = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_recall, context_precision],
)
# result["faithfulness"] = 0.87 (target: >0.90)
```

### Production LLM Monitoring

Track these metrics:
1. **Latency** (P50/P95/P99 per model)
2. **Token usage** (input/output split, cost)
3. **Error rate** (API errors, rate limits, timeouts)
4. **Output quality drift** (LLM-as-judge score over time)
5. **Prompt cache hit rate** (Anthropic/OpenAI prefix caching)
6. **Refusal rate** (unexpected refusals signal prompt issues)

---

## 9. Prompt Engineering: Current Best Practices

### Prompting Hierarchy (2025)

```
System prompt → establishes persona, constraints, output format
User message → task + context
Few-shot examples → 2-5 high-quality input/output pairs
Chain-of-thought → "Think step-by-step" (for reasoning tasks)
```

### Anthropic Prompt Engineering Guide (2025)

**Be explicit and specific**
- Bad: "Summarize this"
- Good: "Summarize this in 3 bullet points, each under 20 words, focusing on business impact"

**XML tags for structure** (Anthropic models perform best with XML)
```
<document>
{{DOCUMENT_TEXT}}
</document>

<task>
Extract all financial figures mentioned above. Return as JSON array with keys: 
amount, currency, description, date.
</task>
```

**Constitutional AI pattern** (self-critique):
```
First, write a draft response.
Then, critique your draft for: accuracy, completeness, potential harms.
Finally, write a revised response incorporating your critique.
```

**Chain of Thought (CoT) triggers**:
- "Think step-by-step before answering"
- "Let's work through this carefully"
- `<thinking>` tags (Anthropic extended thinking models)

### Extended Thinking (Claude Opus 4.8)

```python
# Extended thinking for hard reasoning problems
response = client.messages.create(
    model="claude-opus-4-8",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000  # Thinking tokens allocated
    },
    messages=[{
        "role": "user",
        "content": "Solve this multi-step reasoning problem..."
    }]
)

# Thinking block is separate from final answer
for block in response.content:
    if block.type == "thinking":
        print("Reasoning:", block.thinking)
    elif block.type == "text":
        print("Answer:", block.text)
```

---

## 10. Code Generation and AI-Assisted Development

### Agentic Coding (2025–2026)

**Current best coding models** (SWE-bench Verified):
1. Claude Sonnet 4.6 / Opus 4.8 (Anthropic)
2. GPT-4.1 (OpenAI)
3. Gemini 2.5 Pro (Google)
4. DeepSeek V3/R1 (open source leader)

**AI coding tools landscape**:
- **Claude Code CLI**: Terminal-first, full codebase context, autonomous execution
- **GitHub Copilot**: IDE integration, best for autocomplete
- **Cursor**: IDE (fork of VS Code), best overall agentic coding experience
- **Windsurf (Codeium)**: VS Code fork with Cascade agent
- **Devin**: Autonomous SWE agent (Cognition)

### Code Review with LLMs

```python
# Structured code review
review_prompt = """
Review this code change for:
1. Security vulnerabilities (OWASP Top 10 2025)
2. Performance issues
3. Logic errors
4. Missing error handling for realistic failure modes
5. Test coverage gaps

Code diff:
<diff>
{diff}
</diff>

Return JSON: {"issues": [{"severity": "critical|high|medium|low", "line": N, "description": "...", "fix": "..."}]}
"""
```

---

## 11. Emerging Patterns (2025–2026)

### Context Length Exploitation

Llama 4 Scout's 10M context, Gemini 2.5 Pro's 1M context changes what's possible:
- Entire codebases in context (no RAG needed for <500K token repos)
- Full document sets for analysis
- Long conversation continuity

**Caveat**: Quality degrades at extreme lengths; "lost in the middle" problem persists; prefer retrieval for structured knowledge bases.

### Prompt Caching

```python
# Anthropic prompt caching (up to 90% cost reduction for static context)
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": very_long_system_prompt,
            "cache_control": {"type": "ephemeral"}  # Cache this prefix
        }
    ],
    messages=[{"role": "user", "content": user_query}],
)
# Subsequent requests with same system prompt hit cache (5x cheaper)
```

### Multimodal Inputs (2025 Standard)

```python
# Vision + text with Anthropic (2025 pattern)
with open("chart.png", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": image_data}},
            {"type": "text", "text": "Analyze the trends shown in this chart and identify anomalies."}
        ],
    }],
)
```

### Batch Processing API

```python
# Anthropic Message Batches API (50% cost reduction vs real-time)
batch = client.messages.batches.create(
    requests=[
        {
            "custom_id": f"request-{i}",
            "params": {
                "model": "claude-sonnet-4-6",
                "max_tokens": 1024,
                "messages": [{"role": "user", "content": document}],
            }
        }
        for i, document in enumerate(documents)
    ]
)
# Poll batch.id until complete; retrieve results
```

---

## 12. Production AI System Architecture

### The Modern AI Application Stack (2026)

```
┌─────────────────────────────────────────────────────────┐
│                    User Interface                        │
│              (Next.js 15 + Vercel AI SDK v5)            │
├─────────────────────────────────────────────────────────┤
│                    API Gateway                          │
│          (rate limiting, auth, usage tracking)          │
├───────────────────┬────────────────────────────────────┤
│   Prompt Router   │         Context Assembly            │
│ (task classifier  │  (RAG retrieval, tool results,     │
│  → model select)  │   conversation history)             │
├───────────────────┴────────────────────────────────────┤
│                    LLM Layer                            │
│    Primary (Claude/GPT-4.1) + Fallback + Batch          │
├─────────────────────────────────────────────────────────┤
│                  Data Layer                             │
│  Vector DB (Qdrant/Pinecone) + Object Store + Cache     │
├─────────────────────────────────────────────────────────┤
│              Observability                              │
│        Langfuse / LangSmith + RAGAS evals               │
└─────────────────────────────────────────────────────────┘
```

### E2B: Code Execution Sandboxes

For AI agents that need to run code safely:

```python
from e2b_code_interpreter import Sandbox

with Sandbox() as sandbox:
    execution = sandbox.run_code("""
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('data.csv')
analysis = df.describe()
plt.figure(figsize=(10, 6))
df['revenue'].plot()
plt.savefig('chart.png')
print(analysis.to_json())
""")

    if execution.error:
        raise RuntimeError(f"Code execution failed: {execution.error}")

    output = execution.text  # stdout
    charts = sandbox.download_file("chart.png")
```

---

## Mental Models for AI Development

**The Capability-Reliability Trade-off**: More capable models are not always more reliable. GPT-o3 is smarter than GPT-4.1 mini but GPT-4.1 mini gives more consistent, predictable outputs for structured tasks. Match the model to the task, not to the prestige.

**Evals Before Features**: The highest-leverage investment in any AI system is a comprehensive eval suite. Without evals, you can't know if model updates help or hurt, you can't safely ship prompt changes, and you can't measure the impact of your engineering work.

**The Context Window Illusion**: Just because a model has a 200K context window doesn't mean it uses it well. Performance degrades beyond ~50K tokens for most models (except Gemini 2.5 Pro which maintains quality further). For structured knowledge, RAG > stuffing context.

**Prompt Versioning is Code Versioning**: Prompts are programs. They should be version-controlled, tested on every change, and deployed with the same rigor as code. A prompt change that degrades output quality in production is a bug.

**The Inference Cost Curve**: Inference costs have dropped 100x in 3 years and will drop another 10x by 2028. What's too expensive today (real-time complex reasoning for every user action) will be cheap enough in 18 months. Build the architecture for where costs will be, not where they are.
