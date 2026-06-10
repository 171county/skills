---
name: ai-current-dev-expert
description: Use this skill when you need expert-level knowledge about building production AI applications using current techniques. Triggers on questions about: prompt engineering (chain-of-thought, few-shot, mega-prompts, system prompt architecture), RAG (retrieval-augmented generation, HyDE, RAPTOR, GraphRAG, self-RAG, chunking strategies), embedding models and vector databases (pgvector, Pinecone, Weaviate, Qdrant, Chroma), fine-tuning (LoRA, QLoRA, DPO, SimPO, Axolotl, Unsloth), inference optimization (GPTQ, AWQ, vLLM, TensorRT-LLM, speculative decoding), structured outputs (Instructor, Outlines, JSON mode), evaluation frameworks (LLM-as-judge, RAGAS, Braintrust, LangSmith, Langfuse), and cost optimization strategies for LLM workloads.
---

# AI Current Development Expert

## Prompt Engineering

### Foundational Principles

**Prompt engineering is not magic, it's engineering.** Treat prompts as code: version-control them, test them, measure them, iterate systematically.

**The four levers of prompt quality:**
1. **Task clarity**: Precisely define what you want (not just the task, but the form, length, tone)
2. **Context richness**: Provide enough context for the model to understand the situation
3. **Examples**: Few-shot examples are the highest-ROI prompt technique
4. **Constraints**: Tell the model what NOT to do as much as what to do

### System Prompt Architecture (Mega-Prompt)

For production systems, a well-structured system prompt is the core engineering artifact.

**Recommended system prompt structure:**
```
## Role and Identity
[Who the model is, what expertise it has, what persona it takes]

## Task Description
[Primary job/function, key capabilities, what success looks like]

## Instructions
[Step-by-step guidance, decision rules, priority ordering when instructions conflict]

## Output Format
[Structure, length, tone, what to include/exclude, format examples]

## Constraints and Guardrails
[What never to do, how to handle edge cases, refusal criteria]

## Examples
[2-5 worked examples of input → ideal output]

## Context
[Relevant background, domain knowledge, current situation]
```

**System prompt anti-patterns:**
- Vague role descriptions ("You are a helpful assistant")
- Missing output format specification (model guesses)
- Contradictory instructions (model picks one)
- Overly long without structure (model attends unevenly)
- No examples for complex tasks

### Chain-of-Thought (CoT) Prompting

**Standard CoT:** "Let's think step by step." Appending this to a prompt improves multi-step reasoning by 30-50% on reasoning benchmarks.

**Zero-shot CoT:** No examples, just the think-step-by-step instruction. Works well for Claude and GPT-4+ class models.

**Few-shot CoT:** Provide examples with reasoning chains.
```
Problem: Jane has 3 apples. She gives 1 to Tom and buys 4 more. How many does she have?
Reasoning: Jane starts with 3. She gives 1 away: 3-1=2. She buys 4 more: 2+4=6.
Answer: 6

Problem: [Your actual problem]
Reasoning:
```

**Self-consistency CoT (Wang et al.):** Generate N CoT reasoning paths independently, take majority vote on final answer. Improves accuracy 10-25% over single CoT for math/logic.

**Tree of Thoughts (ToT):** Explore multiple reasoning branches, evaluate intermediate states, backtrack. Useful for planning, creative tasks, multi-step problem-solving where early mistakes compound.

**ReWOO (Reasoning WithOut Observation):** Plan all tool calls before executing any — reduces token usage 3x for tool-calling workflows.

### Advanced Prompting Techniques

**Prompt chaining:** Break complex tasks into sub-tasks, feed output of one step as input to the next. Benefits: easier to debug, parallelize, and improve individual steps.

**Role prompting:** Assign domain expert role → improves outputs in that domain. "As a senior security engineer with 15 years of experience auditing financial systems..." measurably improves security-focused responses.

**Structured thinking instructions:**
```
Before answering, think through:
1. What is being asked?
2. What information is relevant?
3. What are the main considerations?
4. What are potential mistakes to avoid?

Then provide your answer.
```

**Negative constraints (tell model what NOT to do):**
- "Do not include..." is often clearer than "Include only..."
- "Never use bullet points for this response"
- "Do not speculate about information not provided"

**DSPy and MIPRO (programmatic prompt optimization):**
- DSPy: Declarative prompting framework where you define a signature (input → output) and let the optimizer find best prompts
- MIPRO (Multi-step Instruction and demonstration Proposal/Optimization): Bayesian optimization over prompt space
- Use when: You have eval data and want to optimize prompts automatically rather than manually
- Workflow: Define task → collect labeled examples → run optimizer → evaluate

### Prompt Testing and Versioning
- Store prompts in version control (Git) with semantic versioning
- A/B test prompt variants against labeled eval sets
- Track prompt performance over time (models change with updates)
- Tools: Langfuse prompt management, Humanloop, PromptLayer, OpenAI Evals

---

## Retrieval-Augmented Generation (RAG)

### RAG Architecture Fundamentals

**Why RAG:** Models have knowledge cutoffs and limited context windows. RAG dynamically retrieves relevant information at inference time, extending knowledge without retraining.

**Basic RAG pipeline:**
```
Document corpus → chunking → embedding → vector store
                                                    ↓
Query → embedding → similarity search → top-K chunks → context → LLM → answer
```

**RAG failure modes:**
1. Retrieval doesn't find relevant chunks (recall failure)
2. Retrieved chunks are irrelevant (precision failure)
3. Model doesn't properly use retrieved context (utilization failure)
4. Context too long → model loses information in middle
5. Chunk boundaries cut across important context (chunking failure)

### Chunking Strategies

**Fixed-size chunking:** Split every N tokens, overlap 10-20%. Simple but ignores document structure.
- Chunk size: 200-500 tokens is standard; smaller = higher precision, larger = more context per chunk
- Overlap: 50-100 tokens prevents cutting across sentences

**Semantic chunking:** Split at natural semantic boundaries (paragraphs, sections). Better precision but variable chunk sizes.

**Recursive character text splitter (LangChain):** Try to split by: paragraph → sentence → word → character. Respects natural language boundaries.

**Document-aware chunking:**
- HTML/markdown: Preserve heading hierarchy, split by section
- PDFs: Use layout parsing (LlamaParse, Unstructured.io) to detect tables, headings, figures
- Code: Split by function/class boundaries
- Tables: Serialize tables into natural language rows for better embedding

**Parent-child chunking (small-to-big retrieval):**
- Store small chunks (better precision in retrieval)
- Link to parent larger chunks (better context for generation)
- Retrieve small, send large parent chunk to LLM

**Propositions / atomic chunking (Chen et al.):**
- Decompose documents into atomic factual propositions
- Each proposition is a single verifiable claim
- Higher precision retrieval, better faithfulness

### Embedding Models

**Commercial embedding models:**
| Model | Dimensions | Context | Strength |
|-------|-----------|---------|----------|
| text-embedding-3-large (OpenAI) | 3072 (reducible) | 8K | Best for English, general purpose |
| text-embedding-3-small | 1536 | 8K | Cost-efficient, strong quality |
| Voyage-3 (Anthropic's recommended) | 1024 | 32K | Best for code and technical content |
| Voyage-3-lite | 512 | 32K | Fast, cheap |
| Cohere embed-v4 | 1024 | 512 | Multilingual (100 languages) |

**Open-source embedding models:**
| Model | Dimensions | Strength |
|-------|-----------|---------|
| BGE-M3 (BAAI) | 1024 | Multilingual, multi-functionality, multi-granularity |
| GTE-Qwen2-7B-instruct | 3584 | Instruction-following, long context |
| E5-mistral-7b-instruct | 4096 | Strong MTEB performance |
| nomic-embed-text-v2 | 768 | Open, MoE embedding architecture |

**MTEB (Massive Text Embedding Benchmark):** Standard leaderboard for embedding model evaluation. Check paperswithcode.com/sota/text-embeddings for current leaders.

**Fine-tuning embeddings for your domain:**
- Collect positive pairs (query, relevant passage) from your data
- Train with contrastive loss (InfoNCE)
- Domain-specific fine-tuning typically improves retrieval by 10-25%
- Use sentence-transformers library for efficient fine-tuning

### Hybrid Search and Reranking

**Vector search alone misses lexical matches.** Combine for best results:

**Hybrid search:**
- Dense retrieval (embedding similarity) + sparse retrieval (BM25/TF-IDF)
- Combine scores: Reciprocal Rank Fusion (RRF) is robust default
- RRF formula: `1/(k + rank_dense) + 1/(k + rank_sparse)` where k=60 is common
- Improvement: 5-15% recall over vector-only

**Reranking:**
After initial retrieval (top-50), use a cross-encoder to rerank (return top-5):
- Cross-encoders: Compare query+passage jointly, much more accurate than bi-encoders
- Cohere Rerank 3.5: Best commercial reranker, multilingual
- BGE-Reranker-v2-m3: Open-source, strong multilingual
- Flash Reranker (Jina): Fast, lightweight

**Full hybrid pipeline:**
```
BM25 (top-50) + Dense retrieval (top-50) 
       → Reciprocal Rank Fusion (top-50)
       → Cross-encoder reranker (top-5)
       → LLM with top-5 context
```

### Advanced RAG Architectures

**HyDE (Hypothetical Document Embeddings):**
- Query → LLM generates hypothetical answer → embed the hypothetical answer → retrieve against corpus
- Why it works: Hypothetical answer is closer in embedding space to actual documents than raw question
- Best for: Questions that are very different in form from the documents

**RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval):**
- Build hierarchical summaries: chunk summaries → section summaries → document summaries → corpus summaries
- Index all levels, retrieve across levels
- Best for: Multi-hop questions requiring synthesis across documents

**Self-RAG:**
- Model generates retrieval tokens (retrieve or not)
- Critiques its own retrieved context (is it relevant?)
- Critiques its own generation (is it faithful to context? Is it useful?)
- More compute, higher quality than naive RAG

**CRAG (Corrective RAG):**
- Evaluator model scores retrieved documents (correct/incorrect/ambiguous)
- If incorrect: discard and trigger web search
- Combines retrieval fidelity with fallback to fresh information

**GraphRAG (Microsoft, 2024):**
- Build knowledge graph from corpus (entity extraction → relationship extraction)
- Graph-based retrieval: both local (specific entity neighbors) and global (community summaries)
- Strengths: Multi-hop reasoning, holistic questions about entire corpus
- Cost: 3-10x more expensive than naive RAG to index

**Agentic RAG:**
- RAG as a tool in an agent loop
- Agent can decide when to retrieve, what to search for, how many times to iterate
- Can decompose complex questions into sub-queries, retrieve for each, synthesize
- Frameworks: LangChain agents, LlamaIndex query pipelines

### RAG Evaluation

**RAGAS framework:**
- **Context Precision**: Of retrieved chunks, how many were relevant?
- **Context Recall**: Were all relevant chunks retrieved?
- **Faithfulness**: Is the answer grounded in the retrieved context?
- **Answer Relevancy**: Does the answer address the question?

**RAG-specific evals:**
- TruLens RAG Triad: Answer relevance, context relevance, groundedness
- ARES: Automated evaluation with LLM judges
- BEIR: Retrieval benchmark across 18 datasets

---

## Fine-Tuning and Model Adaptation

### When to Fine-Tune vs. Prompt Engineer vs. RAG
| Approach | Best When | Cost | Time |
|----------|-----------|------|------|
| Prompt engineering | Format/style, few-shot behavior | Low | Hours |
| RAG | Factual knowledge, freshness, citations | Medium | Days |
| Fine-tuning | Domain tone, repeated format, task-specific behavior | High | Days-weeks |
| Combined | High-quality production systems | Very high | Weeks |

**Fine-tune when:** You need consistent output format (instruction following), domain-specific terminology and style, faster inference (smaller fine-tuned model ≈ large base model), removing hallucination for narrow tasks, or want to distill from large model to small.

**Don't fine-tune when:** Problem is solvable with prompting, you don't have quality labeled data (>500 examples minimum), or knowledge needs to be fresh/updated frequently.

### LoRA and QLoRA

**LoRA (Low-Rank Adaptation):**
- Instead of updating all W (billions of params), update ΔW = BA where B ∈ ℝ^(d×r), A ∈ ℝ^(r×k), r ≪ d
- Typical rank r = 4-64; higher rank = more capacity but more memory
- Alpha hyperparameter: scaling factor, typically alpha = 2r
- Only LoRA params stored during training (~1% of base model size)
- At inference: merge LoRA weights back into base model (no latency cost)

**QLoRA (Quantized LoRA):**
- Load base model in 4-bit NF4 quantization (reduces memory 75%)
- Apply LoRA in float16
- Double quantization: also quantize quantization constants
- Enables fine-tuning 70B models on 2x A100 80GB

**LoRA hyperparameter guide:**
- rank: Start with 8-16, increase to 64-128 for complex tasks
- alpha: Set equal to rank (conservative) or 2x rank
- target_modules: Usually `q_proj, v_proj`; add `k_proj, o_proj, gate_proj, up_proj, down_proj` for more capacity
- dropout: 0.0-0.1
- bias: 'none' is standard

### Preference Learning (RLHF Variants)

**DPO (Direct Preference Optimization):**
- Input: Pairs of (chosen, rejected) responses for each prompt
- Optimizes: Implicit reward without separate reward model
- Formula: `L_DPO(π_θ) = -E[log σ(β log(π_θ(y_w|x)/π_ref(y_w|x)) - β log(π_θ(y_l|x)/π_ref(y_l|x)))]`
- Simpler than PPO, more stable training, nearly as effective

**SimPO (Simple Preference Optimization):**
- Removes reference model dependency
- Length-normalized reward prevents length bias
- Margin hyperparameter γ ensures chosen-rejected gap
- Generally outperforms DPO while being simpler

**ORPO (Odds Ratio Preference Optimization):**
- Single-stage: SFT + preference learning in one pass
- No reference model, no separate SFT step
- Efficient for low-resource fine-tuning

**Data quality for preference learning:**
- 1000+ preference pairs minimum for signal
- Quality of chosen > quantity of rejected variety
- Hard negative rejection responses (similar but wrong) > easy negatives
- Human or strong LLM (GPT-4, Claude Opus) as annotation source

### Fine-Tuning Infrastructure

**Axolotl:** Best all-in-one fine-tuning library
- YAML config-driven training
- Supports LoRA/QLoRA, DPO, FlashAttention 2
- Multi-GPU via DeepSpeed ZeRO stages 1/2/3
- Works with Llama, Mistral, Qwen, Phi, Gemma

**Unsloth:** Speed-optimized fine-tuning
- 2x faster training than baseline LoRA, 70% less memory
- Custom CUDA kernels for attention and activations
- Best for single-GPU or limited compute
- Notebook-first UX (Colab/Kaggle compatible)

**TRL (Transformer Reinforcement Learning):**
- HuggingFace library: SFTTrainer, DPOTrainer, ORPOTrainer
- Standard API, broad model support
- Good integration with Accelerate for multi-GPU

**DeepSpeed ZeRO:**
- ZeRO-1: Partition optimizer states
- ZeRO-2: + partition gradients
- ZeRO-3: + partition model parameters
- Use ZeRO-2 for most LoRA fine-tuning; ZeRO-3 for full fine-tuning of large models

**Compute requirements (ballpark):**
| Task | Hardware |
|------|---------|
| QLoRA Llama 4 8B | 1x A100 40GB |
| QLoRA Llama 4 70B | 4x A100 80GB |
| Full fine-tune Llama 3 8B | 2x A100 80GB |
| DPO Llama 3 70B | 8x H100 80GB |

---

## Inference Optimization

### Quantization for Production

**GPTQ (Generative Pre-trained Transformer Quantization):**
- Post-training 4-bit or 8-bit quantization
- Layer-by-layer calibration using Hessian
- Quality: Best 4-bit quantization for quality-speed tradeoff
- Use case: GPU inference with models larger than VRAM

**AWQ (Activation-Aware Weight Quantization):**
- Identifies salient weights (high activation magnitude) and protects them
- Slightly better quality than GPTQ, especially for smaller models
- Recommended for production deployment

**GGUF / llama.cpp:**
- CPU + GPU inference
- Multiple quantization levels: Q4_K_M (sweet spot), Q5_K_M (quality), Q8_0 (near-lossless)
- Essential for local/edge deployment

**Quantization quality hierarchy (best to worst):** FP16 > Q8_0 > Q5_K_M > Q4_K_M > Q4_0 > Q3_K_M > Q2_K

### Speculative Decoding
Dramatically reduces latency for large models:
1. Small "draft" model generates K tokens speculatively
2. Large "verifier" model verifies all K tokens in parallel
3. Accept all matching tokens, reject and continue from first mismatch
4. Net result: 2-3x throughput improvement with identical output quality

**Requirements:**
- Draft model must be same vocabulary as main model
- Draft model should be ≥8x smaller
- Works best for tasks where model output is predictable (boilerplate, code)

**Implementation:**
- vLLM: Built-in speculative decoding support
- `ngram speculative decoding`: Use n-grams from prompt as draft (no draft model needed)

### Serving Frameworks Comparison

| Framework | Throughput | Latency | Quantization | Multi-model | Best For |
|-----------|-----------|---------|-------------|-------------|----------|
| vLLM | Highest | Medium | GPTQ/AWQ/GGUF | Yes | High throughput production |
| TensorRT-LLM | Very High | Lowest | FP8/INT4 | Partial | NVIDIA, latency-critical |
| SGLang | High | Low | GPTQ/AWQ | Yes | Structured generation |
| TGI (HuggingFace) | Medium | Medium | GPTQ/AWQ | Yes | Easy deployment |
| Ollama | Low | Medium | GGUF | Yes | Local development |
| Groq | Very Low | Ultra-low | Native | No | Single model, fastest |

**vLLM key features:**
- PagedAttention: KV cache in non-contiguous memory, eliminates fragmentation
- Continuous batching: Dynamic batching as requests arrive
- Chunked prefill: Better GPU utilization for mixed short/long requests
- `tensor_parallel_size` for multi-GPU

### Prompt Caching
**Anthropic prompt caching:** Cache prefix of prompt → subsequent requests with same prefix skip prefill computation. Up to 90% cost reduction and 85% latency reduction.

**Implementation:**
```python
client.messages.create(
    model="claude-opus-4-8",
    system=[{
        "type": "text",
        "text": "Long system prompt...",
        "cache_control": {"type": "ephemeral"}  # Cache this prefix
    }],
    messages=[{"role": "user", "content": user_message}]
)
```

**When to cache:** System prompts >1024 tokens, document analysis with fixed docs, long few-shot examples.

**OpenAI automatic prompt caching:** Automatic for prompts >1024 tokens; 50% discount on cached input tokens. No explicit annotation needed.

**Gemini implicit caching:** Similar behavior on Google Cloud.

### Model Routing for Cost Optimization
Route queries to appropriate model based on complexity:
- Simple queries (classification, extraction): GPT-4o mini, Claude Haiku, Gemini Flash
- Medium queries (summarization, Q&A): Claude Sonnet, GPT-4o
- Complex queries (reasoning, coding, analysis): Claude Opus, GPT-4o with reasoning

**Routing signals:**
- Query length
- Task type classification (classification/summarization/reasoning/generation)
- Cost budget per request
- SLA requirements (latency vs quality)

**Tools:**
- RouteLLM (LMSys): Classifier-based routing with quality guarantee
- Not Diamond: Cloud routing service with quality/cost optimization
- Custom classifier fine-tuned on your traffic

**Cost impact:** 40-70% cost reduction with intelligent routing vs. always using flagship model.

---

## Structured Outputs and Function Calling

### Instructor Library
Most popular structured output library for Python:

```python
import instructor
from anthropic import Anthropic
from pydantic import BaseModel

class UserInfo(BaseModel):
    name: str
    age: int
    email: str

client = instructor.from_anthropic(Anthropic())

user = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Extract: John Smith, 35, john@example.com"}],
    response_model=UserInfo,
)
# user.name == "John Smith", user.age == 35
```

**Instructor features:**
- Automatic retry with error feedback on validation failure
- Streaming partial objects
- Multi-provider support (OpenAI, Anthropic, Mistral, Gemini)
- Iterable extraction (extract list of entities)
- Parallel tool calling via `Iterable[Model]`

### Outlines Library
Grammar-constrained generation for open-source models:

```python
import outlines

model = outlines.models.transformers("mistralai/Mistral-7B-v0.1")

# JSON schema constrained generation
generator = outlines.generate.json(model, UserInfo)
user = generator("Extract user info from: John Smith, 35")
```

**Outlines capabilities:**
- Regular expression constrained generation
- JSON Schema constrained generation
- Grammar (CFG) constrained generation
- Multiple choice (select from options)

**When to use Outlines vs. Instructor:**
- Instructor: Commercial APIs (OpenAI, Anthropic) — uses tool calling/JSON mode
- Outlines: Local models (llama.cpp, transformers) — constrains token logits directly

### OpenAI JSON Mode and Structured Outputs
```python
# Structured outputs (strict, with schema)
response = client.beta.chat.completions.parse(
    model="gpt-4o",
    messages=[...],
    response_format=UserInfo,  # Pydantic model
)

# JSON mode (less strict, just ensures valid JSON)
response = client.chat.completions.create(
    model="gpt-4o",
    response_format={"type": "json_object"},
    messages=[{"role": "user", "content": "Return user info as JSON"}]
)
```

---

## Evaluation Frameworks

### LLM-as-Judge

**G-Eval framework:**
- Define evaluation criteria
- Use GPT-4/Claude as judge with Chain-of-Thought
- Score 1-5 or binary (pass/fail)
- Highly correlated with human ratings

**Judge prompt template:**
```
You are evaluating an AI system's response.
Criteria: [criterion name]
Definition: [what this means]

Question: {question}
Response: {response}
Reference: {reference_answer}  # optional

Score from 1-5:
1 = [definition]
5 = [definition]

Think step by step, then give your score.
Score: [1-5]
Rationale: [brief explanation]
```

**Multi-criteria evaluation:**
- Correctness: Is the answer factually accurate?
- Completeness: Does it fully address the question?
- Faithfulness: (RAG) Is it grounded in retrieved context?
- Coherence: Is the response logically structured?
- Conciseness: No unnecessary filler?

**LLM-as-judge bias mitigation:**
- Position bias: Always put reference answer second
- Verbosity bias: Explicitly instruct judge to ignore length
- Self-enhancement bias: Don't use same model as judge that you're evaluating
- Use 3-5 judge calls, average scores

### RAGAS for RAG Evaluation

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision

result = evaluate(
    dataset,  # HuggingFace Dataset with: question, answer, contexts, ground_truth
    metrics=[faithfulness, answer_relevancy, context_precision],
)
# Returns dict of metric scores
```

**RAGAS metrics breakdown:**
- **Faithfulness**: Extracts claims from answer, checks each against context (0-1)
- **Answer Relevancy**: Generates questions from answer, checks similarity to original question (0-1)
- **Context Precision**: Checks if retrieved context ranks relevant chunks higher (0-1)
- **Context Recall**: Checks if ground truth is covered by retrieved context (0-1)

### Evaluation Platforms

**Braintrust:**
- Production eval platform with CI/CD integration
- Prompt playground + evaluation framework
- Logging, tracing, and experiment comparison
- Strong for teams with multiple prompt variants

**Langfuse:**
- Open-source observability + evaluation
- LLM tracing with cost attribution
- Human annotation workflows
- A/B testing prompts with statistical significance
- Self-hostable (important for enterprise)

**LangSmith (LangChain):**
- Native integration with LangChain ecosystem
- Dataset management, evaluation runs
- Automated testing in CI/CD
- Best for LangChain-based applications

**Arize Phoenix:**
- ML observability → LLM observability
- Embedding drift detection
- RAG debugging with attention visualization
- Good for teams already using Arize for ML

### Eval Best Practices

**Golden dataset construction:**
1. Collect 50-200 real user queries from production
2. Have domain experts write ideal reference answers
3. Cover: common cases, edge cases, failure modes, hard negatives
4. Split: 80% eval set, 20% held-out test set
5. Refresh quarterly as distribution shifts

**Regression testing:**
- Run full eval suite on every prompt change
- Alert on ≥5% regression in any metric
- Compare new model versions before switching

**Behavioral testing (beyond metrics):**
- Adversarial inputs: prompt injection, jailbreaks, out-of-distribution
- Fairness: Evaluate across demographic groups, languages
- Robustness: Typos, paraphrases, different styles

**Production monitoring:**
- Log all inputs/outputs with trace IDs
- Sample 1-5% for human review
- Automated failure detection (empty responses, error strings, safety violations)
- Latency, cost, error rate dashboards

---

## LLM Application Architecture Patterns

### Context Management

**Long context strategies:**
- **Stuffing**: Put everything in context (simplest, expensive, works for <200K tokens)
- **RAG**: Dynamic retrieval (better for large corpora)
- **Summarization**: Compress older context as conversation grows
- **Map-reduce**: Process chunks in parallel, combine results

**"Lost in the middle" problem:** Models attend less to information in the middle of long contexts. Mitigation:
- Put most important context at beginning and end
- Use self-RAG to re-retrieve relevant chunks
- Break into smaller focused contexts

### Multi-Turn Conversation Management
```python
class ConversationManager:
    def __init__(self, max_tokens=8000, summary_threshold=6000):
        self.messages = []
        self.max_tokens = max_tokens
        self.summary_threshold = summary_threshold
    
    def add_message(self, role, content):
        self.messages.append({"role": role, "content": content})
        if self._estimate_tokens() > self.summary_threshold:
            self._summarize_history()
    
    def _summarize_history(self):
        # Keep last N turns verbatim + summary of earlier context
        ...
```

**Memory types in LLM apps:**
- **In-context**: Current conversation (ephemeral)
- **External (RAG)**: Vector store of past interactions
- **Episodic**: Summary of past conversations
- **Semantic**: Extracted facts about user preferences
- **Procedural**: Learned system behaviors (few-shot examples from feedback)

### Streaming and Real-Time Patterns

```python
# Anthropic streaming
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": prompt}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

**When to stream:**
- User-facing chat interfaces (always stream)
- Long document generation (stream to reduce perceived latency)
- Don't stream: Structured output extraction (need complete response), automated pipelines

### Token Budget Management

```python
# Cost estimation before call
import tiktoken

def estimate_tokens(text: str, model: str = "gpt-4o") -> int:
    enc = tiktoken.encoding_for_model(model)
    return len(enc.encode(text))

def cost_estimate(input_tokens: int, output_tokens: int, model: str) -> float:
    pricing = {
        "claude-opus-4-8": (15.0, 75.0),  # $/1M input, output
        "claude-sonnet-4-6": (3.0, 15.0),
        "claude-haiku-4-5": (0.25, 1.25),
        "gpt-4o": (2.5, 10.0),
        "gpt-4o-mini": (0.15, 0.60),
    }
    inp_price, out_price = pricing[model]
    return (input_tokens * inp_price + output_tokens * out_price) / 1_000_000
```

**Cost optimization checklist:**
- [ ] Enable prompt caching for system prompts >1024 tokens
- [ ] Use model routing for different complexity tiers
- [ ] Reduce output tokens with format instructions (don't pad answers)
- [ ] Batch similar requests when latency allows
- [ ] Cache deterministic outputs (same input → cache response)
- [ ] Set appropriate max_tokens (many calls use much less)
