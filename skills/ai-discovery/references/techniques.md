# Emerging AI Techniques Reference

## Reasoning Techniques

### Chain-of-Thought (CoT) Prompting
**What it is:** Prompting a model to produce intermediate reasoning steps before a final answer, rather than answering directly.

**Performance impact:** On GSM8K math problems, PaLM 540B went from 17.9% to 56.9% accuracy (+39 points) with CoT. Models using 10,000+ reasoning tokens achieve 15-25% higher scores than those limited to 1,000 tokens on hard benchmarks.

**Variants:**
- **Zero-shot CoT**: Simply add "Let's think step by step" — works surprisingly well
- **Few-shot CoT**: Provide worked examples with reasoning traces
- **Auto-CoT**: Automatically generate demonstrations via clustering
- **Self-consistency CoT** (see below)

**When it works best:** Multi-step arithmetic, symbolic reasoning, commonsense chains. Least useful for factual recall or simple lookup tasks.

**2025 development:** Research shows that pruning redundant verification steps from long CoT actually *improves* accuracy while reducing length — models sometimes overthink. The art is identifying which reasoning steps are load-bearing.

### Self-Consistency
**What it is:** Generate N diverse reasoning chains independently (parallel sampling, not greedy decoding), then take the majority vote on the final answer.

**Performance:** 5-10 parallel chains improve reliability by 10-15%. Self-consistency with broad sampling reaches 74% on GSM8K, significantly above single-chain CoT.

**Mechanism:** Complex problems often have multiple valid reasoning paths. Greedy decoding can get stuck in one local optimum. Sampling explores the reasoning space.

**Cost:** N× inference cost. Best used when accuracy is paramount and latency/cost is secondary.

### Tree of Thoughts (ToT)
**What it is:** Extends self-consistency into a tree search — model generates multiple partial solution steps, evaluates them, and pursues the most promising branches while pruning dead ends.

**Analogy:** CoT is a single path through a maze. Self-consistency tries multiple paths in parallel. ToT actively explores the tree and backtracks.

**Best for:** Search problems, planning, optimization, puzzles where partial solutions can be evaluated. Less beneficial for tasks where the reasoning is linear.

**Cost:** Much higher than CoT — requires many inference calls. Use selectively.

---

## RAG and Retrieval Techniques

### Standard RAG
Query → embed → semantic search → top-k chunks → inject into context → generate.

**Limitations:** Semantic search retrieves semantically similar text, not necessarily the most relevant. Fails on multi-hop reasoning (needing to connect fact A to fact B to answer). Chunk size is a constant trade-off: large chunks = more context noise; small chunks = missing context.

### HyDE (Hypothetical Document Embedding)
**What it is:** Before retrieval, prompt the LLM to generate a *hypothetical* answer document. Embed that hypothetical document for retrieval instead of the raw query.

**Why it works:** A hypothetical answer is semantically closer to the actual answer documents than the question is. Bridges the query-document gap in embedding space.

**Best for:** Fact-seeking queries where the question form differs greatly from answer form. ("What year did X happen?" vs. documents that contain "X happened in [year]...")

**Limitation:** Adds one LLM call latency. If the hypothetical answer is wrong (hallucinated), it can retrieve misleading documents.

### RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval)
**What it is:** Recursively cluster text chunks, generate summaries at each level, building a hierarchical tree. At retrieval time, do coarse-to-fine retrieval across levels of the tree.

**Why it matters:** Standard RAG retrieves at one granularity. RAPTOR enables queries that need both high-level structure (document-level understanding) and low-level specifics (exact passage retrieval) simultaneously.

**Best for:** Large document corpora (books, codebases, long reports). Multi-granularity queries like "What is the overall argument of this paper AND what did they find in experiment 3?"

**Cost:** Pre-processing intensive (building the tree). But retrieval is then efficient.

### GraphRAG (Microsoft)
**What it is:** Construct a knowledge graph from source documents using an LLM (entity extraction, relationship extraction). Retrieval traverses the graph — enables multi-hop reasoning over connected entities.

**Comparison with standard RAG:** Standard RAG: "Find text about Topic X." GraphRAG: "Find all entities related to X, then find what they're connected to, then synthesize."

**When GraphRAG wins:** Global queries that require synthesizing information across many documents. Example: "What are the common themes across all these research papers?" or "How do the key entities in these documents relate to each other?" — standard RAG can't aggregate across the corpus, but GraphRAG can traverse the community structure.

**When standard RAG wins:** Local queries against well-scoped documents. GraphRAG's graph construction is expensive (LLM calls for every chunk) and overkill for focused factual retrieval.

**Benchmark finding (2025):** GraphRAG outperforms standard RAG on global/thematic queries by 20-40% but performs comparably or worse on specific factual lookups. The overhead is only justified when global synthesis is the main use case.

### ColBERT / Late Interaction
**What it is:** Instead of compressing a document to a single embedding (bi-encoder), ColBERT retains token-level embeddings and computes relevance as the maximum similarity between each query token and all document tokens ("MaxSim").

**Why it matters:** Captures fine-grained token-level relevance. A query about "Apple the fruit" vs. "Apple the company" is disambiguated at token level, not compressed away in a single vector.

**Performance:** Consistently outperforms dense bi-encoders on complex queries; approaches cross-encoder quality at much lower inference cost (no joint encoding of query+doc required at index time).

**Trade-off:** Larger index (stores token vectors, not just one vector per document). 10-100x more storage than standard dense retrieval.

**Use when:** Retrieval precision is critical and you can afford the storage. Especially good for technical/specialized corpora where terminology matters.

### Agentic RAG (2025 State of the Art)
**What it is:** The retrieval decision itself is made by the LLM during its reasoning process. The model decides when to retrieve, what to query, how many times to iterate, and when it has enough information.

**Contrast with pipeline RAG:** Pipeline RAG retrieves once at the start. Agentic RAG retrieves multiple times, reformulates queries based on what it learns, knows when to stop.

**Performance:** 2025 results show modest-sized (7-13B), agent-controlled RAG systems rivaling much larger closed-book LLMs on knowledge-intensive tasks, with full provenance (citations).

**Key papers:** ReAct (Reason+Act), Toolformer, WebGPT established foundations. 2025 frameworks like LangGraph and LlamaIndex Workflows operationalize this pattern.

---

## Architecture Innovations

### Mixture of Experts (MoE)
**What it is:** Instead of one feedforward network per transformer layer, have N "expert" feedforward networks and a router that selects K experts per token (typically K=2 or K=8 out of 16-128 total experts).

**Why it's transformative:** Decouples total parameter count from compute cost. A model with 671B total parameters but only 37B active (like DeepSeek-V3) has the knowledge capacity of a ~671B model but the inference cost of a ~37B model.

**State of the art:** MoE is now the dominant architecture for frontier models. GPT-4, Gemini 1.5/2.x, Llama 4 (16 and 128 experts), DeepSeek-V3/R1, Mixtral — all use MoE.

**Trade-offs:**
- **Expert load balancing**: If the router sends too many tokens to one expert, you lose the efficiency benefit. Requires auxiliary loss terms.
- **Serving complexity**: All experts must fit in memory (or be swapped), increasing memory requirements vs. a dense model of equal active params.
- **Fine-tuning**: More complex than dense models — expert routing can shift during training.

**Key architectural variants:**
- **Switch Transformer**: K=1 (one expert per token) — simplest, proven at scale
- **Mixtral-style (8x7B, 8x22B)**: K=2 of 8 experts — most common balance
- **DeepSeek-V3**: K=8 of 256 experts (fine-grained MoE) — allows more specialization
- **Llama 4 Maverick**: 128 experts — very fine-grained routing

### Multi-head Latent Attention (MLA) — DeepSeek Innovation
**What it is:** Compress key-value (KV) cache representations to a lower-dimensional latent space before attention. At inference, decompress.

**Why it matters:** KV cache is a bottleneck for long-context inference — it grows with sequence length and must stay in GPU memory. MLA reduces KV cache memory by ~10x vs. standard multi-head attention, enabling much longer contexts at lower memory cost.

**Adoption:** First deployed at scale in DeepSeek-V3/R1. Expected to be widely adopted given dramatic memory efficiency gains.

### Speculative Decoding
**What it is:** Use a small "draft" model to generate N candidate tokens in parallel, then verify them all with the large "verifier" model in a single forward pass. Accept/reject tokens based on the verifier's probability.

**Speed gain:** 2-4x throughput improvement with zero quality loss (it's mathematically lossless — rejected tokens are just redrafted).

**Challenge with MoE:** MoE models activate different experts per token, making speculative decoding with MoE more complex. Recent papers (MoE-Spec, MoESD) address this with expert budgeting — predicting which experts will be needed to parallelize expert loading.

**Practical state:** Speculative decoding is now standard in production inference stacks (vLLM, TensorRT-LLM, Hugging Face TGI). Most frontier model APIs use it internally.

### Diffusion Language Models — Emerging Alternative to Autoregressive Generation

The standard approach: autoregressive (AR) generation, one token at a time, left to right. Diffusion offers a fundamentally different paradigm.

**How it works:** Text is corrupted (masked or noised) according to a forward process, then a neural network learns to reverse this process in multiple denoising steps.

**Key models and trajectory:**
- D3PM (2021): 2-3x worse perplexity than GPT-2 — too far behind AR
- SEDD (2023): Matched GPT-2 — ICML 2024 Best Paper. Score Entropy Discrete Diffusion.
- MDLM (2024): Within 14% of AR LMs — NeurIPS 2024. Masked diffusion with cosine schedule.
- LLaDA (2025): 8B-parameter masked diffusion, competitive with LLaMA3 on downstream tasks
- **Gemini Diffusion** (2025): Entered production. ~1,500 tokens/second — roughly 5× faster than autoregressive generation.

**Why diffusion LLMs matter:**
- **Speed**: Parallel denoising can be faster than serial AR at generation
- **Bidirectional context**: The denoising network sees the full sequence, not just left-to-right
- **Controllability**: Easier to condition on partial sequences (infilling, constrained generation)

**Current limitations:** Still slightly below frontier AR models on open-ended generation quality. Best for tasks where controllability and speed matter over raw generation quality (code completion, structured output).

**Watch:** Gemini Diffusion entering production signals that diffusion LLMs are no longer research toys. Expect rapid capability gains in 2025-2026.

---

## Post-Training Techniques

### RLHF / RLAIF
Reinforcement Learning from Human (or AI) Feedback. The core technique for aligning LLM behavior to human preferences. A reward model trained on human comparisons guides policy optimization.

**RLAIF** (from AI feedback) scales annotation without human labelers. Anthropic's Constitutional AI uses a set of principles for the AI to self-critique.

**2025 state:** RLHF is table stakes. The differentiation is now in *what* the reward model rewards — helpfulness, safety, reasoning quality, instruction-following — and *how much* RL is applied (DPO, PPO, GRPO variants).

### GRPO (Group Relative Policy Optimization) — DeepSeek's Key Innovation
Standard RLHF uses a separate reward model. GRPO computes rewards by comparing outputs within a group — no separate reward model needed.

**Effect on DeepSeek-R1:** Incentivizes the model to self-verify, self-correct, and develop extended reasoning chains through reinforcement, not supervision. The model "discovered" reasoning strategies (like chain-of-thought) emergently through RL.

**Significance:** Demonstrates that RL alone, without supervised CoT demonstrations, can produce high-quality reasoning. Cheaper and more scalable than generating human-labeled reasoning traces.

### Supervised Fine-Tuning (SFT) + RL Stack
State-of-the-art post-training uses a staged pipeline:
1. **SFT on high-quality demonstrations**: Teach the model the format and basic task adherence
2. **Reward model training**: Train a separate model to score outputs
3. **RL optimization**: Use PPO, DPO, GRPO, or similar to optimize policy against reward
4. **Rejection sampling / Constitutional filtering**: Remove outputs violating safety constraints

The exact mix varies by lab and is a major source of undisclosed competitive differentiation.
