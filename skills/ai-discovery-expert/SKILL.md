---
name: ai-discovery-expert
description: Use this skill when you need expert-level knowledge about discovering, tracking, and evaluating new AI models, research papers, benchmarks, and capabilities. Triggers on questions about: the latest AI model releases (Claude, GPT, Gemini, Grok, Llama, Mistral, Qwen, DeepSeek), benchmark interpretation (MMLU, GPQA, SWE-bench, HLE, MATH, ARC-AGI), research paper discovery methodology (arXiv, Papers With Code, Semantic Scholar), hardware landscape (NVIDIA H200/GB200, AMD MI300X, Google TPU v6, custom ASICs), AI architecture trends (MoE, SSM, test-time compute scaling, constitutional AI, mechanistic interpretability), video/multimodal AI, AI governance and regulation tracking (EU AI Act, NIST RMF, US AI EO), and competitive intelligence on AI labs and frontier model development.
---

# AI Discovery Expert

## AI Model Landscape (2025-2026)

### Frontier Model Rankings and Capabilities

**The frontier tier (as of mid-2026):**

**Anthropic Claude family:**
- Claude Opus 4.8: Top-tier reasoning, extended thinking mode (>10min compute), 200K context, strongest at complex multi-step reasoning, code, and alignment. ~$15/1M input tokens
- Claude Sonnet 4.6: Performance/cost optimal for most tasks. 200K context, strong coding and analysis. ~$3/1M input tokens
- Claude Haiku 4.5: Fast and cheap, suitable for classification, summarization, routing. ~$0.25/1M input tokens
- Claude differentiators: Constitutional AI training, strongest instruction following, safest for enterprise deployment

**OpenAI GPT family:**
- GPT-5.5 (o3-class): Strongest chain-of-thought reasoning, excels at math/science olympiad problems
- GPT-5 (omni): Multimodal voice, vision, and text; real-time audio with emotional tone
- GPT-4o mini: Low-cost, fast, suitable for production workloads
- OpenAI differentiators: DALL-E 4 image generation, Sora 2 video, operator/user system, strong tool use

**Google DeepMind Gemini family:**
- Gemini 3.1 Pro: 2M context window (highest in industry), strong multimodal, excellent at document understanding
- Gemini 3.1 Flash: Speed/cost optimized, strong for streaming applications
- Gemini Nano: On-device, embedded in Android 16 and Pixel 10
- Google differentiators: Search integration, Google Workspace native, longest context, real-time grounding

**Meta Llama family:**
- Llama 4 (Scout/Maverick/Behemoth): Open weights, MoE architecture, 10M token context (Scout), natively multimodal
- Llama 4 Behemoth: 288B activated params, beats GPT-5 on MMLU at lower cost
- Meta differentiators: Open weights → fine-tuning freedom, commercial use allowed, massive community

**xAI Grok family:**
- Grok 4.3: Real-time Twitter/X data access, voice mode, strong reasoning
- Grok 4.3 Heavy: Thinking model with extended reasoning chains
- xAI differentiators: Real-time internet data from X, direct API on demand pricing

**Chinese frontier models:**
- Qwen 3.5 (Alibaba): Strong multilingual, 32B MoE competitive with GPT-4o, free weights available
- DeepSeek R2: Efficient training methodology ($6M total compute cost claim), strong STEM reasoning
- Baidu ERNIE 5.0, Zhipu GLM-5: Competitive in Chinese language tasks
- Differentiator consideration: Data sovereignty, Chinese regulatory compliance, cost efficiency

**Open-weight competitive tier:**
- Mistral Large 3 (Mistral AI): Strong European model, available via API + self-host
- Falcon 3 (TII): Arabic-first multilingual, strong on low-resource languages
- Command R+ (Cohere): Enterprise RAG-optimized, citation-rich outputs

### Benchmark Ecosystem

**Understanding benchmarks requires knowing their limitations.** Saturation happens quickly — models often achieve >90% on benchmarks within 18 months of benchmark publication.

**Reasoning and knowledge benchmarks:**
| Benchmark | Tests | Top Score Range | Saturation Risk |
|-----------|-------|-----------------|-----------------|
| MMLU | Massive multitask language understanding, 57 subjects | 90%+ (frontier models) | High — largely saturated |
| GPQA Diamond | Grad-level PhD questions (biology, chemistry, physics) | 75-85% (frontier) | Medium |
| HLE (Humanity's Last Exam) | Hardest human-created questions across domains | 40-60% (frontier) | Low — designed to resist saturation |
| MATH-500 | Competition mathematics | 95%+ (o3-class) | High |
| ARC-AGI | Abstract reasoning/generalization (Chollet challenge) | ~65% (best 2025) | Low — by design |

**Coding benchmarks:**
| Benchmark | Tests | Top Score Range |
|-----------|-------|-----------------|
| SWE-bench Verified | Real GitHub issues (500 verified tasks) | 65-75% (agent scaffolds) |
| SWE-bench Lite | 300 curated real GitHub issues | 55-65% |
| HumanEval | Python function completion (164 problems) | ~95% (saturated) |
| LiveCodeBench | Rolling live competitive programming | 55-70% |
| Aider leaderboard | Real-world coding with agentic edits | Model-dependent |

**Multimodal benchmarks:**
- MMMU: Massive Multidisciplinary Multimodal Understanding — college-level visual QA
- Video-MME: Video understanding, long-form video comprehension
- DocVQA: Document visual question answering
- OCRBench: OCR accuracy and understanding

**Agent and tool-use benchmarks:**
- GAIA: General AI assistants benchmark (web browsing, tool use, multi-step tasks)
- AgentBench: LLMs as agents across 8 environments
- ToolBench: Tool-use with 16K+ real-world APIs
- WebArena: Web agent tasks (shopping, forum, code, wiki)
- τ-bench (tau-bench): Retail/airline customer service agent evaluation

**Long context benchmarks:**
- RULER: Realistic long-context tasks (haystack, multi-hop)
- HELMET: Long-context evaluation suite
- ∞Bench: Infinite-length context evaluation (100K+ tokens)

**Benchmark methodology red flags:**
- Contamination: Test data appearing in training data → inflated scores
- Gaming: RLHF tuning specifically for benchmark style
- Distribution shift: Benchmark doesn't reflect real use case distribution
- Missing uncertainty: No confidence intervals = individual scores meaningless

### Model Selection Framework
**Decision tree for model selection:**
1. **Context length required?** → >1M tokens: Gemini 3.1 Pro only; >200K: Claude/GPT-5
2. **Open weights required?** → Llama 4, Qwen 3.5, Mistral Large 3
3. **Cost-sensitive at scale?** → Gemini Flash, Claude Haiku, GPT-4o mini, DeepSeek R2
4. **Best reasoning/math?** → o3-class OpenAI or Claude Opus with extended thinking
5. **Real-time data?** → Grok 4.3 (X data), Gemini (Google search integration), Perplexity
6. **Enterprise compliance?** → Azure OpenAI, Anthropic Claude for Enterprise, Google Vertex AI
7. **On-device/edge?** → Gemini Nano, Llama 4 quantized, Phi-4 mini
8. **Multilingual/CJK?** → Qwen 3.5, ERNIE 5.0, Mistral Large 3

---

## Research Discovery Methodology

### Primary Research Channels

**arXiv (arxiv.org):**
- Primary venue for AI/ML research (cs.AI, cs.LG, cs.CL, cs.CV, cs.NE)
- Preprints published before peer review — get insights months ahead of conferences
- Submissions at 8pm ET Monday/Thursday for next-day publication
- Search strategies: semantic search via Semantic Scholar, keyword filtering
- Alert tools: arXiv daily digest, arxiv.org RSS feeds, AlphaSignal alerts

**Papers With Code (paperswithcode.com):**
- Links papers to open source implementations
- SOTA leaderboards across 4,000+ tasks
- Methods database for architectural components
- Datasets database for training/eval data

**Semantic Scholar (semanticscholar.org):**
- AI-powered academic search (built by Allen Institute for AI)
- Citation graph analysis, influence scores
- Research feed based on interests
- API available for programmatic access

**Hugging Face:**
- Model Hub: 600K+ models, download stats indicate popularity
- Datasets: 100K+ datasets
- Spaces: Live demos to test models immediately
- Papers page: Community curation of important papers
- Trending models/datasets — real-time adoption signal

**Social media and communities:**
- Twitter/X: @papers_daily, @_akhaliq, @levelsio, @karpathy — curated paper sharing
- Reddit r/MachineLearning, r/LocalLLaMA, r/singularity — community discussion
- Discord: EleutherAI, HuggingFace, Nous Research servers
- YouTube: Yannic Kilcher (paper explanations), Two Minute Papers, AI Explained
- Substack: The Batch (Andrew Ng), Import AI (Jack Clark), The Algorithmic Bridge

**Conference proceedings:**
- NeurIPS (December): Neural Information Processing Systems — top-tier ML
- ICML (July): International Conference on Machine Learning
- ICLR (May): International Conference on Learning Representations
- ACL/EMNLP/NAACL: Natural language processing conferences
- CVPR/ICCV/ECCV: Computer vision conferences
- RSS, CoRL: Robotics conferences (increasingly relevant for embodied AI)

### Research Paper Reading Framework

**Tier 1 scan (5 minutes):**
1. Title, abstract, conclusion
2. Figures and tables
3. Results/experiments section
4. Is this incremental or breakthrough?

**Tier 2 study (30 minutes):**
1. Introduction + related work
2. Method section architecture
3. Ablation study (what actually matters)
4. Limitations section (honest papers have one)
5. Appendix details

**Tier 3 deep dive (2+ hours):**
1. Reproduce results if possible
2. Read referenced papers
3. Study code implementation
4. Run on your own data

**Key sections to scrutinize:**
- **Experimental setup**: Data, compute, hyperparameters — small changes matter
- **Baselines**: Are they fair? Are they the right baselines?
- **Statistical significance**: Error bars, standard deviations, multiple runs
- **Limitations**: What did they not test? What fails? Honest limits increase credibility

### Capability Tracking Framework

**The capability radar (track monthly):**
1. **Reasoning**: Math olympiads, logic puzzles, HLE scores
2. **Coding**: SWE-bench, competitive programming, code generation quality
3. **Multimodal**: Image understanding, chart reasoning, video comprehension
4. **Context length**: Needle-in-haystack, multi-hop reasoning over long docs
5. **Tool use / Agency**: GAIA, WebArena scores; task completion rates
6. **Efficiency**: Cost-per-task, latency, context-per-dollar
7. **Safety/Alignment**: Jailbreak resistance, instruction following, refusal quality
8. **Multilingual**: Coverage of non-English languages

---

## AI Architecture Trends

### Mixture of Experts (MoE)
The dominant architecture for cost-efficient large models.

**How it works:** Multiple "expert" sub-networks; a routing layer selects top-K experts for each token. Only active experts use compute → more parameters at lower inference cost.

**Key papers:**
- Mixtral 8x7B (2023): Mainstream MoE availability
- Switch Transformer (Google, 2021): Foundational paper
- Gating mechanisms: Top-K sparse routing, expert choice routing, balanced load balancing

**2025 MoE trends:**
- Llama 4 Scout: 17B active, 109B total — 10M context via iRoPE
- DeepSeek V3/R1: MoE with 37B active, 671B total — training cost efficiency
- Qwen 3.5 MoE: 22B active out of 235B total params
- Expert specialization: Evidence that experts develop domain specializations

**Engineering considerations:**
- Expert load balancing critical (prevent expert collapse)
- Memory bandwidth is the bottleneck, not compute
- Quantization strategies differ for MoE vs dense

### State Space Models (SSMs)
Alternative to attention mechanism for long sequences.

**Key architectures:**
- **Mamba**: Selective SSM with O(n) complexity vs transformer O(n²) — breakthrough for long sequences
- **Mamba-2**: Improved architecture, hardware-aware algorithm
- **RWKV**: RNN-Transformer hybrid, efficient inference
- **H3**: Hybrid SSM + attention

**SSM vs. Transformer tradeoffs:**
| Aspect | SSM (Mamba) | Transformer |
|--------|-------------|-------------|
| Training throughput | Slower | Faster |
| Inference (long context) | O(n) — much faster | O(n²) — slow |
| Recall on long sequences | Weaker | Stronger |
| State compression | Fixed-size state | Full KV cache |

**2025 trend:** Hybrid architectures combining SSM for long context efficiency + attention for recall quality. Jamba (AI21), Falcon Mamba, Griffin (Google DeepMind).

### Test-Time Compute Scaling
The paradigm shift of 2024-2025: scaling inference-time compute improves performance, not just training compute.

**OpenAI o1/o3 discovery:** Allocating more inference tokens for chain-of-thought ("thinking") dramatically improves difficult reasoning tasks.

**Methods:**
- **Chain-of-Thought (CoT)**: Model generates reasoning steps before answer
- **Self-consistency**: Generate N answers independently, take majority vote
- **Tree of Thoughts (ToT)**: Explore multiple reasoning branches
- **Monte Carlo Tree Search (MCTS)**: RL-inspired search over token space
- **Best-of-N sampling**: Generate N completions, rank by reward model
- **Process Reward Models (PRMs)**: Score intermediate reasoning steps (vs outcome reward models)

**Extended thinking models (2025):**
- Claude Opus 4.8 with extended thinking
- OpenAI o3-pro
- Google Gemini 2.5 Deep Think
- Tradeoff: Higher latency and cost for better accuracy

**Implications:**
- Math and coding tasks see largest gains (ARC-AGI: 0% → 65%+ with compute scaling)
- Diminishing returns vary by task type
- Budget forcing: constrain thinking tokens to manage cost/quality tradeoff

### Constitutional AI and RLHF Variants
**RLHF (Reinforcement Learning from Human Feedback):**
- Human raters rank model outputs → train reward model → RLHF fine-tune
- Limitations: Expensive, rater inconsistency, Goodhart's Law (optimize proxy, lose alignment)

**Constitutional AI (Anthropic):**
- Self-critique using a written constitution (set of principles)
- RLAIF: AI-generated preference labels instead of human raters
- CAI pipeline: SL-CAI → RL-CAI → final model
- Advantage: Scalable, consistent, interpretable principles

**DPO (Direct Preference Optimization):**
- Simplifies RLHF by removing explicit reward model
- Directly optimizes policy using preference pairs
- Widely adopted in open-source fine-tuning (simpler than PPO)

**IPO, SimPO, ORPO:** DPO variants addressing length bias, reference-free optimization, odds ratio preference

**Reward hacking / alignment tax:**
- Over-optimization on human preferences can decrease actual quality
- KL divergence penalty limits how far from base model
- Best practice: Diverse rater pools, iterative feedback loops

### Mechanistic Interpretability
The science of understanding what neural networks compute internally.

**Key findings (2024-2025):**
- **Superposition**: Models represent more features than dimensions by encoding multiple features in overlapping directions
- **Sparse autoencoders (SAEs)**: Decompose model activations into interpretable features (Anthropic's "features" work)
- **Circuits**: Identified specific circuits in GPT-2 (induction heads, indirect object identification)
- **Universality**: Similar circuits emerge across different models trained independently
- **Linear representation hypothesis**: World model concepts encoded linearly in activation space

**Anthropic's interpretability milestones:**
- Mapping millions of features in Claude 3 Sonnet
- Identified "features" for cities, famous people, programming constructs
- Steering vectors: Modify model behavior by activating specific feature directions
- Golden Gate Claude: Injected "Golden Gate Bridge" feature to create obsessed behavior

**Research tools:**
- TransformerLens: Mechanistic interpretability library (Neel Nanda)
- Baukit: Activation patching and steering
- SAELens: Sparse autoencoder training and analysis

---

## Hardware and Infrastructure Landscape

### NVIDIA Ecosystem
**H200 SXM (2024):** 141GB HBM3e memory, 3.35TB/s bandwidth, FP8 performance. Primary training workhorse for large models.

**Blackwell architecture (GB200 NVL72, 2025):**
- 72 GPUs in a rack with NVLink 5.0 high-speed interconnect
- 5:1 GPU:CPU ratio (30 CPUs, 72 GPUs per rack)
- 20 petaFLOPs FP4 per GPU
- NVLink bandwidth: 1.8TB/s per GPU (vs 900GB/s in Hopper)
- Target: Training runs of 100K+ GPUs

**NVIDIA software moat:**
- CUDA ecosystem: 20+ year lead, 3,500+ libraries
- cuDNN, NCCL, TensorRT — battle-tested production software
- Dynamo inference stack (2025): Dynamic serving for multi-node inference

### AMD and Alternatives
**AMD MI300X:**
- 192GB HBM3 (beats H100's 80GB significantly)
- Large memory enables large batch inference without offloading
- ROCm software maturing — still lags CUDA ecosystem significantly
- Growing adoption: Meta, Microsoft Azure, Groq

**AMD MI350X (2025):**
- 288GB HBM3e, 8-stack
- Targeting H200/GB200 performance

**Intel Gaudi 3:**
- 128GB HBM2e, targeting training and inference
- Lower cost than NVIDIA, but smaller ecosystem

### Google TPU Infrastructure
**TPU v5p:**
- 459 TFLOPS BF16
- 4,096-chip pods interconnected with ICI network
- Google's research uses TPU; external access via Google Cloud
- Key advantage: Tight HW/SW co-design with JAX/XLA

**TPU v6e (Trillium):**
- 2x improvement over v5e per chip
- Optimized for inference at scale

### Custom ASICs and Emerging Players
**Amazon Trainium2:**
- 16-chip clusters, 3.1 EFLOPS
- AWS-native, lowest cost for AWS training workloads

**Groq LPU (Language Processing Unit):**
- Deterministic latency architecture
- Up to 500 tokens/second inference speed
- Batch limitation: optimized for single-stream, not batch throughput

**Cerebras WSE-3:**
- Wafer-scale engine — single chip wafer
- 900K AI cores, 44GB on-chip SRAM
- Eliminates memory bandwidth bottleneck

**SambaNova SN40L:**
- Reconfigurable dataflow architecture
- Strong on memory-bandwidth-bound inference tasks

**Etched Sohu:**
- ASIC specifically for transformer attention
- Claims 10x efficiency over GPU for transformer workloads

### Inference Optimization
**Quantization:**
- FP16: Standard training/inference
- BF16: Better numeric range for training, same memory
- INT8: 2x memory reduction, ~5% quality loss
- GPTQ (4-bit): Weight quantization post-training, high quality
- AWQ (4-bit): Activation-aware weight quantization — best quality/compression
- GGUF (ggml format): CPU/GPU inference via llama.cpp

**Attention optimization:**
- FlashAttention 3: Hardware-aware CUDA kernel, 1.5-2x speedup
- PagedAttention (vLLM): KV cache paging eliminates fragmentation
- Sliding window attention: O(n) for long sequences
- Ring attention: Distributed attention across devices

**Serving frameworks:**
- vLLM: Highest throughput, PagedAttention, continuous batching
- TensorRT-LLM (NVIDIA): Optimized for NVIDIA hardware, best latency
- TGI (HuggingFace): Production-ready, broad model support
- Ollama: Local inference, excellent UX
- SGLang: Structured generation with efficiency optimizations

---

## Multimodal and Emerging Capabilities

### Vision-Language Models (VLMs)
**Architecture evolution:**
- CLIP (2021): Contrastive image-text pretraining — foundational
- LLaVA: Open-source vision instruction tuning
- PaliGemma 2 (Google): Compact VLM, strong visual QA
- Moondream 2: Tiny but capable (1.86B params)
- Qwen2.5-VL: Strong on charts, documents, video frames

**Key VLM capabilities (2025):**
- Chart and diagram understanding (financial charts, scientific plots)
- Multi-image comparison and reasoning
- Video frame analysis (key frame extraction)
- Handwriting and diagram OCR
- Mathematical visual reasoning

### Video AI
**Frontier video generation:**
- **Veo 3.1** (Google DeepMind): Up to 8K, 60fps, physics-consistent, audio synchronized
- **Sora 2** (OpenAI): 1080p, 60 seconds, strong consistency
- **Kling 2.0** (Kuaishou): Strong motion consistency, popular in Asia
- **Runway Gen-4**: Professional-grade, used in Hollywood post-production
- **Pika 2.0**: Consumer-friendly, rapid iteration

**Video understanding:**
- Gemini 3.1 Pro: Native video understanding up to 2M tokens (~2 hours of video)
- Video-LLAVA, LLaVA-NeXT-Video: Open-source video understanding
- Use cases: Security footage analysis, sports analytics, content moderation

### Audio and Speech
**Speech-to-text:**
- Whisper (OpenAI, open source): Dominant for transcription, 99 languages
- AssemblyAI: Production STT with speaker diarization, sentiment
- Deepgram: Low-latency streaming STT for real-time applications
- Nova-3 (Deepgram): Best accuracy for accented speech

**Text-to-speech:**
- ElevenLabs: Highest quality voice cloning, 29 languages
- Cartesia Sonic: Ultra-low latency (100ms), real-time voice
- PlayHT 3.0: Emotional voice control, human-like variation
- Bark (Suno AI): Open-source, expressive speech with music/effects
- Kokoro: Small open-source TTS model, strong quality/size ratio

**Voice AI assistants:**
- GPT-5 voice mode: Real-time emotional audio, interruption handling
- Gemini Live: Mobile-native voice, Google integration
- Claude voice (Anthropic): Emerging capability

### Embodied AI and Robotics
**Foundation models for robotics:**
- RT-2, RT-X (Google): Vision-language-action models for robots
- π0 (Physical Intelligence): Generalist robot foundation model
- OpenVLA: Open Vision-Language-Action model
- Octo: Generalist robotic manipulation policy

**Key robotics companies (2025):**
- Physical Intelligence (π): $400M Series B, Bezos/OpenAI backing
- Figure AI: $675M raised, BMW partnership, OpenAI integration
- Agility Robotics (Amazon): Digit humanoid, Amazon warehouse deployment
- Boston Dynamics: Atlas humanoid going electric, Spot commercial success
- 1X Technologies, Apptronik, Unitree: Competitive humanoid field

---

## AI Governance and Policy Tracking

### EU AI Act
**Full enforcement timeline:**
- February 2, 2025: Prohibited AI practices effective (social scoring, subliminal manipulation, real-time remote biometrics)
- August 2, 2025: GPAI (General Purpose AI) obligations effective (transparency, copyright, systemic risk)
- August 2, 2026: High-risk AI system obligations fully effective (medical devices, critical infrastructure, employment, biometrics)
- August 2, 2027: Legacy systems must comply

**Systemic risk threshold:** 10²⁵ FLOPs training compute. Models above threshold = systemic risk GPAI (GPT-5, Claude Opus 4.8-class).

**GPAI obligations (systemic risk):**
- Model evaluation before deployment
- Adversarial testing (red-teaming)
- Incident reporting to European AI Office
- Cybersecurity protection

### NIST AI Risk Management Framework (AI RMF)
- GOVERN: Establish policies, accountability, culture
- MAP: Identify AI risks in context
- MEASURE: Analyze and assess AI risks
- MANAGE: Prioritize and treat AI risks

**NIST AI 600-1 (Generative AI profiles):** 12 unique risk categories for GenAI including confabulation, CBRN information, data privacy, value chain/component risks.

### US AI Executive Orders
- Biden EO 14110 (Oct 2023): Safety testing, watermarking, federal agency AI use
- Trump EO (Jan 2025): Revoked Biden EO — deregulatory orientation, focus on American AI dominance
- Trump "AI Action Plan" (Dec 2025): Export controls, energy for AI compute, DOD AI investment
- Key regulatory dynamic: US deregulating while EU enforcing — regulatory arbitrage opportunities

### China AI Governance
- Generative AI Regulation (2023, effective Aug 2023): Real-name registration, content labeling, security assessments
- AI Security Governance Framework (2024): Risk classification, red lines for prohibited applications
- Cross-border data flow restrictions: Significant compliance requirement for US companies operating in China

---

## AI Discovery Tools and Infrastructure

### Discovery and Monitoring Tools
| Tool | Purpose | Key Features |
|------|---------|-------------|
| arXiv Sanity | Paper discovery | Visual browsing, recommendation |
| Semantic Scholar | Academic search | Citation analysis, AI-powered |
| Connected Papers | Citation graph | Visual relationship mapping |
| Papers With Code | Implementation tracking | SOTA leaderboards, code links |
| AlphaSignal | AI curated digest | Top papers weekly |
| Elicit | Research assistant | Literature review, claim extraction |
| Consensus | Research synthesis | AI meta-analysis from papers |
| Perplexity | Real-time search | Web + papers, sourced answers |
| AI2 S2ORC | Full paper corpus | 200M papers, full text |

### Model Testing and Evaluation
- **LMSYS Chatbot Arena**: Human ELO-based ranking, 1M+ votes
- **Hugging Face Open LLM Leaderboard**: Automated benchmark suite
- **OpenCompass**: Comprehensive Chinese AI evaluation platform
- **HellaSwag, WinoGrande**: Commonsense reasoning
- **BIG-bench**: 200+ tasks measuring model capabilities

### Staying Current: Recommended Cadence
**Daily (10 min):**
- X/Twitter: Follow @_akhaliq, @karpathy, @ylecun, @sama, @darioamodei
- HuggingFace trending models page
- r/LocalLLaMA top posts

**Weekly (1-2 hours):**
- Papers With Code SOTA updates
- Semantic Scholar recommendations
- The Batch (Andrew Ng) newsletter
- Import AI (Jack Clark) newsletter
- arXiv digest for cs.AI + cs.LG + cs.CL

**Monthly (2-4 hours):**
- Full capability benchmark review
- Model pricing and availability update
- Regulatory landscape check
- Evaluate new models on your specific use cases

**Quarterly:**
- Major conference proceedings review (NeurIPS, ICML, ICLR)
- Investor landscape: who's funded, what thesis
- Hardware roadmap updates (NVIDIA GTC, Google Cloud Next)
- Update your model selection framework
