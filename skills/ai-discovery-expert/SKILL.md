---
name: ai-discovery-expert
description: Expert-level knowledge base for discovering, researching, and evaluating AI/ML breakthroughs, papers, benchmarks, and models. Use this skill when users ask about finding cutting-edge AI research, reading ML papers, tracking model capabilities, understanding benchmarks (MMLU, SWE-bench, LMSYS, HLE), AI safety and alignment research (mechanistic interpretability, Constitutional AI, scalable oversight), open-source vs closed-source model comparison, GPU/inference hardware, building AI discovery pipelines, or evaluating AI systems in production. Invoke for any question about the current frontier of AI research, model evaluation methodology, or staying current with AI developments.
---

# AI Discovery Expert

Expert-level knowledge for discovering, evaluating, and staying current with the AI research frontier (2024–2026).

---

## 1. Core AI Research Discovery Platforms

### arXiv (arxiv.org)
The foundational preprint server. Papers appear within ~24 hours, ahead of peer review — 6–12 months before conference publication. Key categories:
- `cs.LG` — Machine Learning
- `cs.CL` — Computation & Language / NLP
- `cs.CV` — Computer Vision
- `cs.AI` — AI general
- `stat.ML` — Statistical ML

Subscribe to daily digest emails or RSS: `https://export.arxiv.org/rss/cs.LG+cs.CL+cs.CV`

### Papers With Code (paperswithcode.com)
Indexes arXiv papers, links to GitHub repos, and benchmark leaderboards. Start from a task/benchmark leaderboard (e.g., ImageNet Top-1), find SOTA papers, trace through related work. "Methods" pages describe architectures. SOTA tracker is fastest way to see if a method has been surpassed.

### Semantic Scholar (semanticscholar.org)
AI-powered academic search with citation graphs, influence metrics, and AI-generated TLDR summaries. "Highly Influential Citations" filter finds papers that build on foundational work. Best for tracing lineage and understanding paper clusters.

### Hugging Face Papers (huggingface.co/papers)
Community-curated trending papers with upvoting, updated daily. Each paper links to released model weights. The trending feed surfaces papers the community actually implements — strong practical relevance filter.

### AlphaXiv (alphaxiv.org)
Layer on top of arXiv adding community discussion threads, annotation, and author following. Provides asynchronous peer commentary. Founder: Stanford researchers.

### Connected Research Tools
- **Connected Papers**: Visual citation graph for any starting paper — find adjacent work missed by keyword search
- **Elicit**: AI-powered systematic review — upload a research question, extracts claims from relevant papers
- **Litmaps**: Citation-graph visualization for literature reviews
- **ResearchRabbit**: "Spotify for papers" — citation network + semantic recommendations

---

## 2. Paper Reading Methodology

### Three-Pass Method (Still Canonical)
**Pass 1 (5 min) — Triage:** Read title, abstract, introduction headings, conclusion, figures/captions only. Answer: What problem? What approach? What claim? Worth reading fully?

**Pass 2 (1 hour) — Comprehension:** Read fully, skip proofs. Focus on: related work (understand problem space), proposed method (diagrams > equation proofs), experimental setup (what baselines, what datasets), results tables. Mark claims to verify.

**Pass 3 (4–5 hours) — Critique:** For every claim, ask: what would falsify this? Look for: dataset contamination, cherry-picked examples, missing ablations, baseline comparability, compute cost transparency.

### LLM-Era Workflow Enhancements
- Ask an LLM to summarize the abstract and explain key equations
- Use Semantic Scholar's TLDR for rapid first-pass triage
- Cross-reference claimed SOTA against Papers With Code leaderboards in real-time
- Check AlphaXiv discussion threads for community perspective

### Paper Quality Heuristics
**High-quality signals:** Code released on GitHub, ablation studies present, compute/FLOP budget stated, negative results reported, comparison against strong tuned baselines.

**Low-quality signals:** Only comparing to 2-year-old baselines, no error bars, human evaluation on <100 examples, BLEU/ROUGE as sole metric.

---

## 3. Key AI Sub-Domains and Frontiers (2024–2026)

### Large Language Models

**Scaling Laws:** Hoffmann et al. (2022) "Chinchilla" — compute-optimal scaling: model size N and token count D should scale as N ∝ C^0.5 and D ∝ C^0.5. This overturned the "bigger models" narrative. Chinchilla-optimal = ~20 tokens per parameter. Post-2023: many labs train "overtrained" smaller models (e.g., Llama family) for better inference economics.

**Emergent Capabilities:** Wei et al. (2022) documented emergent abilities — tasks jumping from near-random to high accuracy at certain scale thresholds. Schaeffer et al. (2023) argued emergence is an artifact of discontinuous metrics. Practical reality: reasoning tasks show qualitative shifts between 7B and 70B+ scales.

**Post-Training Alignment:**
- **SFT (Supervised Fine-Tuning):** Instruction-following datasets. WizardLM, Orca, Phi models demonstrate data quality beats quantity.
- **RLHF:** Policy + reward model + PPO. Expensive, unstable. Scales worse than pretraining.
- **DPO (Direct Preference Optimization):** Reparameterizes RLHF as supervised classification. Default for open-source alignment. Variants: SimPO, ORPO, IPO.
- **RLAIF:** LLM generates preference labels, replacing human annotators. Constitutional AI (Anthropic) is the paradigm case.

**Test-Time Compute (2024–2026 Frontier):**
- OpenAI o1/o3 and DeepSeek-R1 established that scaling inference compute produces qualitative capability jumps beyond pretraining scale.
- DeepSeek-R1: proved reasoning via pure RL can match o1-level performance.
- Test-time compute scaling enables smaller models to outperform models 4x their size on specific tasks (ICLR 2025).
- OpenAI's 2024 inference spend: $2.3B — 15x GPT-4.5 training cost.

### Multimodal Models

**Vision-Language Models (VLMs):** Architecture convergence — frozen visual encoder (ViT/SigLIP) + projection layer + LLM backbone. Key models: LLaVA, InternVL3, Qwen-VL, DeepSeek-VL2 (up to 1024×1024 resolution for documents/medical). Qwen3-VL-235B approaches GPT-5 and Gemini 2.5 Pro.

**Audio:** Whisper for ASR remains standard. GPT-4o introduced end-to-end audio I/O with prosody understanding.

**Video:** Timeline: Sora (Feb 2024, DiT architecture treating video as spacetime patches) → Veo 2 (Dec 2024, 4K) → Veo 3 (May 2025, native audio) → Sora 2 (Sep 2025, 10–25s videos + synchronized audio).

### Reasoning Models

**Chain-of-Thought (CoT):** Wei et al. (2022). "Think step by step" dramatically improves arithmetic, symbolic reasoning, commonsense tasks. Scales effectively above ~100B parameters.

**o1/o3-Style Reasoning:** RL-trained extended CoT ("thinking tokens") not shown to users. o3 achieved 45.1% on ARC-AGI. AIME improvement: ~15% (GPT-4) → 96% (o3). DeepSeek-R1 (MIT license, Jan 2025): 671B MoE, matches o1-level, open weights.

**Process Reward Models (PRMs):** Reward intermediate reasoning steps rather than final answers. Reduces reward hacking. Foundation for reliable chain-of-thought training.

### Diffusion Models

**Image:** FLUX (Black Forest Labs, 2024) — flow matching architecture, leads open-source. DALL-E 3 improved text adherence via caption improvement. Midjourney v6 for photorealism.

**Video:** Sora uses DiT (Diffusion Transformer) treating video as spacetime patches — first to demonstrate realistic physics understanding.

---

## 4. Benchmark Tracking

### Active Benchmark Matrix

| Benchmark | Domain | Status | Notes |
|---|---|---|---|
| MMLU | General knowledge (57 subjects) | **Saturated** (90%+) | Replaced by MMLU-Pro |
| MMLU-Pro | Harder MMLU, 10-choice | **Active frontier** | Gemini 3 Pro ~90.1%, Claude Opus ~89.5% |
| HumanEval | Python code (164 problems) | **Saturated** (>90%) | Replaced by SWE-bench |
| SWE-bench Verified | Real GitHub issue resolution | **Active** | Frontier agents 40–60% |
| MATH / AIME 2025 | Competition mathematics | **Active** | o3/R1 ~96% on MATH |
| GPQA Diamond | Expert-level science MCQ | **Active** | Top models ~70–75% |
| Humanity's Last Exam (HLE) | Expert crowdsourced hard questions | **Active frontier** | Frontier models <25% |
| FrontierMath | Research-level mathematics | **Active frontier** | Frontier models <2% |
| ARC-AGI | Novel pattern reasoning | **Active** | o3 at 45%, ARC-AGI-2 announced |
| LMSYS Chatbot Arena | Human preference (Elo) | **Gold standard** | Real users, blind A/B battles |
| BFCL v4 | Tool/function calling | **Active** | Critical for agentic systems |
| HELM | 42+ scenarios (Stanford) | **Active** | Multi-dimensional |

### Benchmark Methodology Insights

**Saturation Pattern:** Every benchmark eventually saturates as models overfit to its distribution. MMLU explicitly excluded from leading leaderboards. Pattern recurs ~18 months after wide adoption.

**Contamination:** Models trained on internet data may have seen test sets. Detection: canary strings, held-out evaluation sets, timing correlation with training data cutoffs.

**LMSYS Chatbot Arena is the most externally valid** because it uses blind A/B battles with real users — no contamination possible. Biases: English-centric, verbosity bias, preference may not correlate with accuracy.

**Harder frontiers (2025–2026):** Humanity's Last Exam (<25%) and FrontierMath (<2%) establish that expert-level discovery remains far from solved.

---

## 5. Evaluation and Production Methodology

### LLM-as-Judge
Use a capable LLM (GPT-4o, Claude 3.5 Sonnet) to evaluate against rubrics. 80–92% agreement with human preferences at 500–5000x cost savings. Biases to correct:
- **Position bias:** Favors first or second response
- **Verbosity bias:** Favors longer responses regardless of quality
- **Self-enhancement bias:** Favors outputs stylistically similar to judge model

Mitigation: randomize response order, use multiple judges, calibrate against human ratings.

### Production Evaluation Stack
1. **Offline evals:** Fixed dataset with known labels. Run before every deployment. Tools: Promptfoo (open-source), Braintrust, LangSmith.
2. **Shadow testing:** New model runs in parallel to production; human reviewers compare.
3. **A/B testing:** Route traffic % to new model; measure downstream business metrics + LLM judge scores.
4. **Human preference evaluation:** Side-by-side comparison by expert annotators. Indispensable for calibrating automated methods.

### Golden Dataset Construction
- Sample from real production traffic (not test-set proxies)
- Include edge cases: very short, very long, adversarial, out-of-distribution requests
- Label with multiple annotators; track inter-annotator agreement
- Version and freeze datasets — never modify a golden set once deployed

---

## 6. AI Safety and Alignment Research

### Mechanistic Interpretability

**Core Problem:** Neural networks are opaque. Mechanistic interpretability reverse-engineers learned algorithms into human-understandable circuits.

**Key Concepts:**
- **Superposition:** Models encode more features than neurons by overlapping feature representations — making neurons polysemantic.
- **Sparse Autoencoders (SAEs):** Train SAE to reconstruct layer activations — extracts monosemantic features. Used by Anthropic in "Scaling Monosemanticity" (Templeton et al. 2024) to find interpretable features in Claude 3 Sonnet.
- **Circuits:** Sub-networks implementing specific computations (Induction Heads for in-context learning, Indirect Object Identification circuits).

**Recent Advances:**
- "Scaling Monosemanticity" (Anthropic 2024): millions of interpretable features found in Claude 3 Sonnet via SAEs
- Circuit Tracing paper (transformer-circuits.pub, 2025): Attribution graphs reveal computational structure
- Gemma Scope (DeepMind, 2024): Open SAE suite for Gemma models
- Claude Sonnet 4.5 pre-deployment: mechanistic interpretability used to identify and suppress representations of "evaluation awareness"

### Constitutional AI (CAI)
Anthropic's framework: defines a set of principles governing model behavior.
1. **CAI SL-CAI:** Model critiques and revises its own outputs against the constitution (no human labels needed).
2. **CAI RLAIF:** AI generates preference labels by evaluating which response better follows the constitution.

CAI 2.0 (2026): Dynamic constitution updates — model can propose amendments subject to human oversight. Claimed 40% reduction in harmful outputs vs RLHF-only baselines.

### Scalable Oversight
**Problem:** As AI becomes more capable, humans may lack ability to evaluate outputs (can a human verify a complex proof?).

**Approaches:**
- **Debate (Irving et al.):** Two AI agents argue; human judge decides.
- **Process Reward Models:** Reward intermediate reasoning steps, not just final answers.
- **Recursive Reward Modeling (RRM):** Human oversees using weaker AI as assistant, pyramids capability.

### Key Safety Organizations

| Organization | Focus |
|---|---|
| Anthropic | Constitutional AI, mechanistic interpretability, scaling safety |
| OpenAI Safety | RLHF, scalable oversight, debate, process reward models |
| DeepMind Safety | Specification gaming, robustness, Gemma Scope |
| ARC (Alignment Research Center) | Dangerous capabilities evaluations |
| Center for AI Safety | X-risk, societal harms, model evaluation |

---

## 7. Open Source vs. Closed Source AI (2025–2026)

### The Convergence Story
2025 closed the capability gap: open-source LLMs are now ~6–12 months behind frontier closed-source models (down from ~24 months in 2023).

### Open-Source Ecosystem

**Meta Llama:** Llama 4 (2025) — Maverick/Scout variants, competitive with GPT-4o. Commercial use requires Meta agreement at large scale.

**Mistral:** Mistral 7B (GQA + sliding window attention); Mixtral 8x7B (sparse MoE, 47B total / 13B active); Codestral (code-specialized). Apache 2.0 for smaller models.

**Alibaba Qwen:** Qwen 2.5 72B very competitive; Qwen3 (2025) hybrid MoE architecture, meets/beats GPT-4o. Mostly Apache 2.0.

**Google Gemma:** Gemma 2 (9B/27B), Gemma 3 (updated), Apache 2.0. Gemma Scope provides open interpretability infrastructure.

**DeepSeek:** DeepSeek-V3 (Dec 2024, 671B MoE, ~$6M training cost); DeepSeek-R1 (Jan 2025, MIT license, matches o1, fully open weights + technical report). Distilled variants 1.5B–70B also released.

### Closed-Source Frontier

| Model | Provider | Strengths |
|---|---|---|
| GPT-4o / GPT-5 | OpenAI | Multimodal, coding, instruction following |
| Claude 3.5/4.x | Anthropic | Reasoning, safety, long context, coding |
| Gemini 2.5 Pro | Google | Long context (1M+ tokens), multimodal |
| Grok 3 | xAI | Real-time data access |

### Decision Framework: Open vs. Closed
**Choose open source when:** Data privacy is critical (on-premise), fine-tuning on proprietary data required, cost at scale matters, need model weights for research/interpretability, avoid vendor lock-in.

**Choose closed source when:** Maximum capability needed, complex multimodal requirements, strict latency/reliability SLAs, API costs acceptable vs. infrastructure management.

---

## 8. Hardware and Infrastructure

### GPU Landscape

**NVIDIA H100:** 80GB HBM3, 3.35 TB/s bandwidth, 700W. Industry standard through 2023–2025. Cloud: ~$2–4/hr.

**NVIDIA H200:** 141GB HBM3e, 4.8 TB/s. Drop-in upgrade — 1.83–2.14× H100 throughput in LLM inference. Same form factor, 700W.

**NVIDIA B200 (Blackwell, 2025–2026):** 192GB HBM3e, 8 TB/s, 208B transistors, native FP4. 4× H100 training throughput (claimed), up to 4.9× throughput vs RTX PRO 6000. 1,000W — liquid cooling required. Backordered ~3.6M units through mid-2026.

**AMD MI300X:** 192GB HBM3, strong for memory-bound inference. Growing with ROCm ecosystem improvements.

**Google TPU v5/v5p:** Best for JAX/XLA workloads; available via GCP.

### Inference Optimization

**vLLM:** PagedAttention (virtual memory for KV-cache), continuous batching, tensor parallelism. FP8 quantization with <1–2% quality degradation. The production inference standard.

**TensorRT-LLM (NVIDIA):** Maximum throughput on NVIDIA hardware. Inflight batching, speculative decoding, LoRA hot-swapping.

**Quantization Methods:**
- GPTQ: 4-bit weight-only quantization
- AWQ: Better than GPTQ at same bit width
- GGUF (llama.cpp): Mixed-precision for consumer hardware (Q4_K_M, Q5_K_M)
- FP8: Hardware-native on H100/B200; replaces INT8 as production standard

**Speculative Decoding:** Small "draft" model generates candidates; large model verifies in parallel. 2–4× speedup.

---

## 9. Research Organizations

| Organization | Approach | Notable Work |
|---|---|---|
| **Anthropic** | Safety-first; responsible scaling (ASL tiers) | Constitutional AI, mechanistic interpretability, Claude, Circuit Tracing |
| **OpenAI** | Capabilities + safety team | o1/o3 reasoning, multimodal, RLHF, function calling |
| **Google DeepMind** | Science + product integration | Gemini, AlphaFold, RT-2, Gemma Scope, Chinchilla |
| **Meta FAIR** | Open research + weight publishing | Llama 1/2/3/4, SAM, ImageBind |
| **Microsoft Research** | Efficiency + partnerships | Phi small models, DeepSpeed, BitNet (1-bit LLMs) |
| **Stanford HAI/CRFM** | Academic benchmarking | HELM, Foundation Models report |
| **Berkeley LMSYS** | Open inference infrastructure | Chatbot Arena, FastChat, vLLM |

---

## 10. AI Discovery Pipeline Architecture

### Information Sources by Tier

**Tier 1 — Daily Triage (15 min/day):**
- arXiv daily digest (cs.LG + cs.CL + cs.CV)
- Hugging Face /papers/trending
- Twitter/X: @karpathy, @ylecun, @rasbt, @natolambert, @lilianweng, @fchollet, @jackclarkSF

**Tier 2 — Weekly Synthesis (2 hrs/week):**
- **Import AI** (Jack Clark): Strategic analysis, insider perspective on research + policy
- **The Batch** (Andrew Ng/DeepLearning.AI): Research summaries, educational framing
- **Ahead of AI** (Sebastian Raschka): Deep technical dives, ML practitioner focus
- **Interconnects** (Nathan Lambert): RLHF/post-training specialist, research depth
- Papers With Code SOTA updates for tracked benchmarks

**Tier 3 — Deep Dives (as needed):**
- Connected Papers graph for selected topics
- Semantic Scholar citation analysis
- Full three-pass reading for <5% of triaged papers

**Tier 4 — Community:**
- Alignment Forum (alignmentforum.org): AI safety, highly technical
- Eleuther AI Discord: Open-source research, interpretability reading groups
- Hugging Face Discord: Model releases, implementation discussions

### Automated Discovery Tools
- **Karpathy's autoresearch** (March 2026): 630-line script running hundreds of ML experiments overnight autonomously
- AlphaXiv: Author following + paper discussions
- arXiv RSS + Feedly/Inoreader with keyword filters

---

## 11. Key Evaluation Concepts

### Capability Elicitation
Benchmarks measure a lower bound on capability. Prompting techniques (CoT, few-shot, role specification) can dramatically change apparent performance. True capability requires best-effort elicitation across prompting strategies.

### Red Teaming
Systematically attempt to elicit harmful/dangerous/deceptive outputs. Anthropic pre-deployment assessments include formal red teaming. Claude Sonnet 4.5 pre-deployment included mechanistic interpretability analysis identifying and suppressing "evaluation awareness" representations.

### A/B Testing Framework
1. Hypothesis: expected direction and magnitude of change
2. Traffic split: 10/90 for risky changes, 50/50 for balanced testing
3. Calculate minimum detectable effect before launch
4. Guardrails: hard stops if safety metrics drop >X%
5. Statistical analysis: Mann-Whitney U for non-normal distributions

### The Benchmark Lifecycle
1. Release: differentiates models clearly
2. Adoption: community accepts as standard
3. Saturation: top models plateau, no differentiation
4. Replacement: harder benchmark created

Current saturation: MMLU, HumanEval, BIG-Bench.
Currently active frontier: SWE-bench, GPQA Diamond, HLE, FrontierMath, ARC-AGI-2.
