---
name: ai-discovery-expert
description: Expert-level AI research and discovery knowledge covering the frontier AI landscape (Anthropic, OpenAI, Google DeepMind, Meta), benchmarks (MMLU, HLE, ARC, BIG-Bench), scaling laws (Chinchilla, inference-optimal), alignment techniques (RLHF, DPO, Constitutional AI, GRPO), mechanistic interpretability, transformer architecture (attention, MoE, SSMs/Mamba), RAG variants, multimodal AI, fine-tuning (LoRA/QLoRA), inference optimization (GGUF/AWQ/GPTQ, vLLM, speculative decoding), and reading AI research papers. Use for tracking AI research developments, understanding model capabilities, evaluating AI approaches, or deep-diving into AI technical foundations.
---

# AI Discovery Expert

## The Frontier AI Landscape

### Labs and Their Positions (as of mid-2026)

**Anthropic** — Safety-focused lab; Claude model family. Core technical contributions: Constitutional AI (CAI), interpretability research (superposition, features as linear combinations), RLHF at scale. Claude 4.x family (Haiku 4.5, Sonnet 4.6, Opus 4.8, Fable 5) represents current production line.

**OpenAI** — GPT-4.1, o3, o4 series. Key innovations: RLHF (original scaling), GPT-4V multimodal, o1 reasoning (chain-of-thought at test time / "reasoning models"), real-time audio. Currently leading on coding and reasoning benchmarks.

**Google DeepMind** — Gemini 2.5 Pro/Flash family. Strengths: long context (1M+ token native), multimodal (video, image, audio), integration with Google Search/Maps/Gmail. AlphaFold, AlphaCode pioneered AI in science. Pioneered Transformer architecture (Vaswani et al., 2017).

**Meta AI** — Llama 4 family (Scout, Maverick, Behemoth). Open weights strategy (Meta Open License). Key innovations: Grouped Query Attention (GQA), RoPE positional embeddings. Most important open-weights models for the ecosystem.

**xAI** — Grok models; Colossus cluster. Focus: real-time web access, reasoning, scientific reasoning.

**Mistral AI** — Mixture of Experts (Mixtral 8×7B pioneered MoE at open weights scale), European lab, efficient models.

**Cohere** — Enterprise focus, Command models, RAG-optimized (Rerank), embedding models.

### Model Capability Mapping (2025–2026 frontier)

| Task | Best Current Model |
|------|-------------------|
| Coding (SWE-bench) | Claude Sonnet 4.6, GPT-4.1 |
| Long-context reasoning | Gemini 2.5 Pro (1M tokens) |
| Mathematical reasoning | o3, o4-mini, Gemini 2.5 |
| Instruction following | Claude models (Constitutional AI) |
| Multimodal understanding | GPT-4o, Gemini 2.5 |
| Open weights (overall) | Llama 4 Maverick |
| Speed + cost efficiency | Claude Haiku 4.5, GPT-4o-mini, Gemini Flash |

---

## Benchmarks

### Benchmark Progression and Saturation

Benchmark saturation is one of the most important dynamics in AI evaluation. A benchmark becomes useless when frontier models score >90%—there's no longer signal between models.

**MMLU (Massive Multitask Language Understanding):**
- 57 subjects; multiple-choice undergraduate–professional level
- GPT-3 (2020): 43%; GPT-4 (2023): 86%; Claude 3.5 (2024): 88.7%
- **Status: Saturated** for frontier models. No longer discriminative. Frontier models all cluster 85–90%+.

**HLE (Humanity's Last Exam):**
- 2,500 questions from domain experts across math, science, humanities
- Designed to resist AI saturation
- GPT-4o: ~10%; o3: ~21%; Gemini 2.5 Pro: ~18%
- **Status: Active frontier** — still discriminative

**ARC-AGI (Abstraction and Reasoning Corpus):**
- Novel visual pattern reasoning; requires few-shot generalization
- GPT-4o: ~5%; o3 with compute scaling: ~87%; frontier 2026: 90%+
- **Status: Approaching saturation** at frontier; important test of generalization vs. memorization

**SWE-bench Verified:**
- Real GitHub issues with test suites; measures practical software engineering ability
- Claude Sonnet 4.6 (Opus scaffolded): 70%+; GPT-4.1: 65%+
- **Status: Active frontier**, rapidly improving

**Key principle:** When a benchmark saturates at frontier, the field needs new benchmarks. Track which benchmarks are discriminative vs. saturated to know which results are meaningful.

---

## Scaling Laws

### Chinchilla Scaling Laws (Hoffmann et al., 2022)

Trained on compute-matched experiments with 70 transformer models ranging from 70M to 16B parameters. Key finding:

**Chinchilla optimal training:** Given a compute budget C:
- Model parameters N and training tokens D should scale equally: N ∝ D ∝ C^0.5
- Optimal tokens ≈ 20× model parameters
- Chinchilla (70B) was compute-optimal and outperformed Gopher (280B) trained with same compute

**What this means for practice:**
- GPT-3 (175B, 300B tokens) was significantly under-trained per Chinchilla
- For a given compute budget, train a smaller model on more data
- LLaMA (Touvron et al., 2023) explicitly followed Chinchilla: trained on 1T+ tokens (much larger than Chinchilla optimal for the model size) to make an inference-optimal model

### Inference-Optimal Scaling

The Chinchilla framework optimizes for training compute. But in production, **inference cost matters more than training cost** for deployed models.

**Inference-optimal regime:** Train smaller models on more tokens than Chinchilla-optimal → smaller model, same capability, lower inference cost.

LLaMA's approach: 7B model trained on 1T+ tokens is better than Chinchilla-optimal 70B model for applications where you need to serve billions of requests.

**2025 shift: Test-time compute scaling.** OpenAI's o-series models (o1, o3) and Google's Gemini 2.5 show that you can trade inference compute for quality—more "thinking steps" = better answers. This is a new dimension of the scaling laws.

```
Training-time scaling: More data + bigger model + more compute during training
Test-time scaling: More inference compute per query (chain-of-thought, self-verification, tree search)
```

---

## Training and Alignment Techniques

### RLHF (Reinforcement Learning from Human Feedback)

**Standard pipeline:**
1. **Supervised Fine-Tuning (SFT):** Fine-tune pretrained model on demonstration data (human-written ideal responses)
2. **Reward Model Training:** Train a model to predict human preferences from comparison data (human ranks response A vs. B)
3. **PPO (Proximal Policy Optimization):** Fine-tune SFT model using RL with the reward model as the reward signal

**Challenges:**
- Reward hacking: Model learns to game the reward model rather than actually improving
- Distribution shift: Human raters inconsistent; hard to collect at scale
- Expense: Human labeling is the bottleneck

### DPO (Direct Preference Optimization)

Rafailov et al. (2023). Eliminates the explicit RL step by reformulating the RLHF objective as a classification loss.

```
Loss = -E[(log σ(β * log(π_θ(y_w|x)/π_ref(y_w|x)) - β * log(π_θ(y_l|x)/π_ref(y_l|x))))]

Where:
  y_w = preferred response ("winner")
  y_l = dispreferred response ("loser")
  π_θ = policy being trained
  π_ref = reference policy (frozen SFT model)
  β = temperature parameter (controls deviation from reference)
```

**Practical effect:** DPO is simpler, more stable, cheaper than PPO-based RLHF, and achieves comparable or better alignment results. Used by most open-weight models (LLaMA-3, Mistral).

### Constitutional AI (CAI) — Anthropic

Trains the model to critique and revise its own responses according to a set of principles ("constitution").

**Two phases:**
1. **SL-CAI (Supervised Learning):** Generate responses, use the same model to critique and revise per principles, use revised responses as supervised training data
2. **RL-CAI:** Use a Constitution-trained reward model (trained on AI comparisons, not human comparisons) for RL feedback

**Key insight:** Can reduce dependence on human feedback for harmful content classification; AI feedback at scale. Enables safety training without massive human labeling of harmful examples (which is psychologically damaging for human labelers).

### GRPO (Group Relative Policy Optimization)

DeepSeek's contribution; used in DeepSeek-R1 and derivatives. Avoids the need for a separate value model (saves memory). Uses the mean reward of a group of outputs as the baseline.

**Relevance:** Enabled training reasoning capabilities (chain-of-thought) at lower compute cost than PPO. Has become a standard alternative to PPO for reasoning model training.

---

## Transformer Architecture Deep Dive

### Attention Mechanism

```
Attention(Q, K, V) = softmax(QK^T / √d_k) × V

Where:
  Q = query vectors (what to look for)
  K = key vectors (what each token has)
  V = value vectors (what to pass forward)
  d_k = dimension of key vectors (scaling prevents softmax saturation)
```

**Multi-Head Attention (MHA):** Run h attention heads in parallel on projected subspaces, concatenate and project output. Each head can focus on different aspects of relationships.

**Computational cost:** O(n²) in sequence length n for attention. This is the fundamental bottleneck for long-context inference.

### Key Architecture Innovations

**Grouped Query Attention (GQA):** Used in LLaMA-3, Mistral. Multiple query heads share key/value heads. Reduces KV cache memory at inference by 4–8× with minimal quality loss.

**Flash Attention (Dao et al., 2022; v2 2023; v3 2024):**
- Reorders attention computation to be I/O-aware: uses SRAM rather than HBM for intermediate results
- No approximation: exact attention, but fast (~3× faster than baseline)
- Enables training with much longer sequences (SRAM is fast; HBM bandwidth is the bottleneck)

**Rotary Positional Embeddings (RoPE):**
- Encodes position information into rotations of query/key vectors
- Enables length extrapolation (model can generalize to sequences longer than training)
- Standard in modern models (LLaMA, Gemini, Mistral)

**ALiBi (Attention with Linear Biases):**
- Adds linear bias to attention logits based on distance
- Simple alternative to learned positional embeddings; extrapolates well

### Mixture of Experts (MoE)

Sparsely-activated feedforward networks: model has N experts but only activates K per token (K << N).

```
MoE output = Σ G(x)_i × Expert_i(x)
Where G(x) = softmax(W_g × x) selects top-K experts by weight
```

**Key properties:**
- Total parameters >> active parameters per token
- Training cost ≈ dense model with #active_params parameters
- But model quality ≈ dense model with #total_params parameters
- Caveat: Expert routing load balancing requires careful attention

**Production examples:**
- Mixtral 8×7B: 8 experts, 2 active per token; 46B total params, 13B active
- GPT-4: Believed to use MoE (unconfirmed)
- Gemini 1.5: MoE architecture confirmed

### State Space Models (SSMs / Mamba)

Alternative to attention for sequence modeling. Addresses O(n²) attention complexity.

**Mamba (Gu & Dao, 2023):** SSM with input-selective state transitions. Linear time in sequence length. Outperforms Transformers of equal size on many language benchmarks.

**Mechanism:** 
```
h_t = A(x_t) × h_{t-1} + B(x_t) × x_t  (selective state update)
y_t = C(x_t) × h_t                        (output)
```

**Hybrid architectures:** Jamba (AI21 Labs), Zamba combine attention layers with SSM layers—get long-context efficiency from SSMs, complex reasoning from attention.

**Status (2025–2026):** Pure SSM models are not yet frontier for complex reasoning; hybrid architectures are emerging. Long-context efficiency advantage makes them attractive for specific use cases.

---

## Mechanistic Interpretability

### Core Research Questions

Mechanistic interpretability tries to reverse-engineer neural networks: what are the actual algorithms learned, what features are computed, how do circuits implement behaviors?

**Superposition hypothesis (Anthropic):** Neural networks can represent more features than they have neurons by using nearly-orthogonal directions in activation space. Features are linear combinations of neurons, not individual neurons.

**Evidence:** Toy models trained on sparse, synthetic data show that networks learn polysemantic neurons (encoding multiple features) and monosemantic neurons—with the balance determined by feature sparsity and the number of true features vs. neurons.

**Sparse Autoencoders (SAEs):** Method to "decompress" superposed representations into interpretable features. Train a sparse autoencoder to reconstruct model activations; the SAE learns to use sparse, interpretable directions.

### Circuits

A "circuit" is a minimal computational subgraph that implements a specific behavior.

**Induction heads:** One of the first circuits found; implement in-context learning by attending to previous token occurrences. Found in every transformer studied; likely foundational to in-context learning.

**IOI (Indirect Object Identification):** *"When Mary and John went to the store, John gave the bag to ___"* → "Mary". Anthropic reverse-engineered a circuit implementing this in GPT-2.

**Practical implications:**
- Debugging unexpected model behavior by tracing activations
- Understanding what knowledge is stored where
- Targeted fine-tuning of specific circuits
- Verifying safety claims: does the model "know" it's doing something dangerous?

---

## Fine-Tuning: LoRA and QLoRA

### LoRA (Low-Rank Adaptation)

Hu et al. (2021). Freeze pretrained weights; add low-rank decomposition matrices to each weight matrix.

```python
# Without LoRA: W is [d_out × d_in]
output = x @ W.T  # full parameter update

# With LoRA: W = W_0 + ΔW = W_0 + B @ A
# A is [r × d_in], B is [d_out × r], where r << min(d_out, d_in)
output = x @ W_0.T + x @ A.T @ B.T  # only A and B are trained
```

**Key hyperparameters:**
- **r (rank):** Typical range 8–64. Higher rank = more parameters = more expressiveness. Start with 8–16.
- **α (alpha):** Scaling factor for LoRA update. Often set to 2r (r=8, α=16 is a common default). Effective learning rate = α/r × lr.
- **target_modules:** Which weight matrices to adapt. Common: query + value (q_proj, v_proj). Adding k_proj, o_proj, and MLP layers improves quality at modest parameter cost.

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(
    r=16,
    lora_alpha=32,  # alpha = 2r
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj",
                    "gate_proj", "up_proj", "down_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)
model = get_peft_model(base_model, config)
model.print_trainable_parameters()
# trainable params: 41,943,040 || all params: 6,738,415,616 || trainable%: 0.6228
```

**Memory savings:** 7B model full fine-tune = ~56GB VRAM. With LoRA r=16 = ~8GB VRAM.

### QLoRA

Dettmers et al. (2023). Combines quantization + LoRA:
1. Quantize base model to 4-bit NF4 (Normal Float 4-bit—optimal for normally distributed weights)
2. Add LoRA adapters on top (full float32 or bfloat16 for adapters)
3. Use double quantization (quantize the quantization constants for additional memory savings)
4. Use paged optimizers to prevent OOM spikes

**Memory savings:** 70B model fine-tune with QLoRA on single 48GB A6000 GPU. Made large model fine-tuning accessible.

```python
from transformers import BitsAndBytesConfig
import torch

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16
)
model = AutoModelForCausalLM.from_pretrained(model_id, quantization_config=bnb_config)
```

---

## RAG (Retrieval-Augmented Generation) Variants

### Basic RAG vs. Advanced RAG

**Naive RAG:** Query → embed → cosine similarity → top-k chunks → LLM. Works for simple factoid Q&A; breaks on complex, multi-hop reasoning.

**Advanced RAG techniques:**

**Hybrid Search (BM25 + Vector):**
```python
from langchain.retrievers import EnsembleRetriever
from langchain_community.retrievers import BM25Retriever

bm25_retriever = BM25Retriever.from_documents(docs, k=5)
vector_retriever = vectorstore.as_retriever(search_kwargs={"k": 5})

ensemble = EnsembleRetriever(
    retrievers=[bm25_retriever, vector_retriever],
    weights=[0.5, 0.5]
)
# BM25 captures exact term matching; vector captures semantic similarity
# Reciprocal rank fusion (RRF) combines the ranked lists
```

**Parent-Child Chunking:**
- Index small "child" chunks for precise retrieval
- Return large "parent" chunks for full context to LLM
- Child size ~100–200 tokens; parent size ~500–1000 tokens

**Reranking (Cross-Encoder):**
- Initial retrieval (fast, approximate) returns top 20–50 candidates
- Cross-encoder reranks: reads each doc-query pair together (slow but precise)
- Return top-3 to LLM

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import CrossEncoderReranker
from langchain_community.cross_encoders import HuggingFaceCrossEncoder

reranker = HuggingFaceCrossEncoder(model_name="BAAI/bge-reranker-v2-m3")
compressor = CrossEncoderReranker(model=reranker, top_n=3)
retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vector_retriever
)
```

**HyDE (Hypothetical Document Embeddings):**
- Generate a hypothetical answer to the query → embed that → retrieve similar actual documents
- Improves recall for queries that are phrased differently from how they're stored

**Multi-Query Retrieval:**
- Generate 3–5 paraphrases of the query → retrieve for each → deduplicate by union
- Improves recall for queries that miss relevant documents with original phrasing

**RAGAS Evaluation Framework:**
```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision, context_recall

results = evaluate(
    dataset=test_dataset,
    metrics=[faithfulness, answer_relevancy, context_precision, context_recall]
)
# faithfulness: Does the answer follow from the context?
# answer_relevancy: Does the answer address the question?
# context_precision: Are retrieved docs relevant?
# context_recall: Were all necessary docs retrieved?
```

---

## Inference Optimization

### Quantization Formats

| Format | Bits | Quality | Speed | Typical Use |
|--------|------|---------|-------|-------------|
| FP32 | 32-bit | Best | Slowest | Training |
| BF16 | 16-bit | Near-lossless | Fast | Serving, fine-tune |
| INT8 | 8-bit | Very good | Faster | Production inference |
| GPTQ | 4-bit | Good | Fast | GPU local serving |
| AWQ | 4-bit | Good | Fast | GPU local serving |
| GGUF/Q4_K_M | 4-bit | Good | Fast | CPU/GGML inference |
| GGUF/Q2_K | 2-bit | Degraded | Fastest | Memory-constrained |

**AWQ vs. GPTQ:** Both 4-bit. AWQ (Activation-aware Weight Quantization) outperforms GPTQ at low bit-widths; faster dequantization. GPTQ uses layer-by-layer Hessian-based quantization. In practice: use AWQ if available, GPTQ otherwise.

**GGUF:** llama.cpp format; runs on CPU + GPU hybrid; widely supported by Ollama, LM Studio.

### vLLM and PagedAttention

**The KV cache problem:** During autoregressive generation, keys and values for all previous tokens must be cached. With large batch sizes and long sequences, the KV cache is the memory bottleneck.

**PagedAttention (Kwon et al., 2023):** Treats KV cache like OS virtual memory—divides into blocks, stores non-contiguously, eliminates fragmentation. Enables:
- Near-zero KV cache memory waste
- 2–4× higher throughput than HuggingFace Transformers
- Efficient memory sharing for beam search, parallel sampling

```python
# vLLM deployment
from vllm import LLM, SamplingParams

llm = LLM(
    model="meta-llama/Llama-4-Scout",
    dtype="bfloat16",
    tensor_parallel_size=2,  # Split across 2 GPUs
    gpu_memory_utilization=0.90,
    max_model_len=8192
)

sampling_params = SamplingParams(temperature=0.7, top_p=0.95, max_tokens=512)
outputs = llm.generate(["Tell me about transformers", "Explain MoE"], sampling_params)
```

**Continuous batching:** Unlike static batching (wait for full batch), continuous batching allows new requests to join mid-batch. Dramatically improves throughput with variable-length requests.

### Speculative Decoding

**Problem:** LLM generation is memory-bandwidth bound (each token requires full model forward pass = slow).

**Speculative decoding:** Use a fast, small "draft" model to generate K candidate tokens, then verify all K with the large model in a single forward pass.

- Draft model: 7B or smaller
- Target model: 70B or larger
- If draft is correct: free tokens (accepted without additional target model forward pass)
- If incorrect: Fall back to target model token

**Speedup:** 2–3× faster generation for the large model with no quality degradation. Effective when draft model agrees with target model >80% of the time (typical for instruction-following).

---

## Reading AI Research Papers

### Paper Structure and Navigation

1. **Abstract:** Read first—does this paper address what you think it does?
2. **Figures:** Before reading body text, scan all figures and their captions. Most papers' key contributions are visualized.
3. **Introduction:** Problem statement, why existing approaches fail, contribution summary.
4. **Related Work:** Positions paper in the literature; helps understand what's new.
5. **Methods:** The core technical contribution. Hardest to understand; requires re-reading.
6. **Experiments:** How they evaluated; what baselines they compared against. Check baselines carefully—weak baselines make contributions look bigger than they are.
7. **Results:** The numbers. Look for statistical significance, error bars, ablations.
8. **Ablation studies:** Which components of the method actually matter? Ablations answer this.
9. **Limitations/Conclusion:** Authors' honest assessment of what doesn't work or what they'd do differently.

### Evaluating Paper Quality

**Red flags:**
- No ablation studies (can't tell which components matter)
- Baselines not state-of-the-art or not properly tuned
- Only one dataset or benchmark (may be cherry-picked)
- Results not statistically significant
- Reproducibility not addressed; no code released
- Claims far outpace evidence in introduction/abstract

**Green flags:**
- Multiple diverse evaluation benchmarks
- Strong baselines with fair hyperparameter tuning
- Code released or under review
- Ablation studies present
- Limitations section is honest
- Human evaluation for generation tasks (not just automatic metrics)

### Key Venues and Preprint Culture

**Top ML conferences:** NeurIPS (December), ICML (July), ICLR (May), ACL/EMNLP (NLP focus).

**Preprints:** arXiv cs.LG, cs.AI, cs.CL see most frontier research first (often 6–12 months before conference publication). Not peer-reviewed—track the authors and their institutions.

**Hugging Face Hub:** Models, datasets, demos. Papers page tracks research with direct code/model links.

**Tracking frontier:** arXiv weekly digests, Papers With Code (rankings + reproducibility), LessWrong/Alignment Forum (alignment research), Twitter/X (fast dissemination of new results).

---

## Multimodal AI

### Architecture Patterns

**Early fusion:** Combine raw modalities before the main model (concatenate tokens).
**Late fusion:** Encode each modality separately, combine at the output.
**Cross-attention:** One modality attends to another through cross-attention layers (most common in VLMs).

**Vision-Language Models (VLMs):**
1. Vision encoder (ViT—Vision Transformer) processes image into patch embeddings
2. Projector/connector maps visual embeddings to language model token space
3. Language model processes combined visual + text tokens

**LLaVA architecture:**
```
Image → CLIP ViT encoder → MLP projector → [visual tokens + text tokens] → LLaMA
```

**GPT-4V, Claude vision, Gemini:** All use similar principles; differences in vision encoder quality, resolution, how visual tokens are compressed.

### Video Understanding

**Challenges:** Temporal reasoning, long-form video (thousands of frames), computational cost.

**Approaches:**
- Frame sampling: select keyframes; computationally cheap but misses temporal dynamics
- Video-specific temporal encoders (Video ViT, TimeSFormer)
- Gemini 2.5 Pro handles video natively via efficient video encoding

---

## Quick Reference: AI Research Landscape

**Active research frontiers (2025–2026):**
1. Test-time compute scaling (o-series reasoning models)
2. Long-context architectures (1M+ token efficient processing)
3. Multimodal models (video, audio, 3D)
4. Mechanistic interpretability
5. Agentic capabilities (planning, tool use, multi-step)
6. AI-accelerated drug discovery (AlphaFold successors)
7. Smaller, inference-optimal models (quantization, distillation)
8. Synthetic data generation for training

**Model evaluation hierarchy (discriminative → saturated):**
HLE > ARC-AGI > SWE-bench Verified > MMLU (saturated at frontier) > TriviaQA (saturated)

**Recommended reading path for AI fundamentals:**
1. Attention Is All You Need (Vaswani et al., 2017) — Transformer architecture
2. Scaling Laws for Neural Language Models (Kaplan et al., 2020)
3. Training language models to follow instructions with human feedback (Ouyang et al., 2022) — RLHF
4. Chinchilla paper (Hoffmann et al., 2022)
5. Constitutional AI (Bai et al., 2022) — Anthropic alignment
6. Direct Preference Optimization (Rafailov et al., 2023)
7. QLoRA (Dettmers et al., 2023)
8. Llama 2 / Llama 3 technical reports (Meta)
9. Mamba (Gu & Dao, 2023) — SSMs
10. Efficient Memory Management for LLM Serving (PagedAttention, Kwon et al., 2023)
