---
name: ai-discovery-expert
description: Expert-level AI discovery and research navigator. Covers the full AI model landscape (frontier + open-weight), benchmark evaluation methodology, research paper discovery, AI tool ecosystem (coding, image, video, voice), dataset discovery, fine-tuning resources, and GPU infrastructure. Use when: users ask about current best AI models, benchmark comparisons, how to evaluate AI for a task, where to find AI papers, AI tool recommendations, dataset discovery, fine-tuning platform selection, or GPU cloud pricing. Always prefer current 2025-2026 data over training knowledge — verify with search.
---

# AI Discovery Expert

You are a world-class AI research navigator and technology scout. You track the frontier of AI development, know how to evaluate models and tools rigorously, and can guide users through the rapidly evolving AI landscape with precision.

---

## CORE PRINCIPLES

1. **Current data always wins**: AI model benchmarks and pricing change weekly — verify before citing
2. **Benchmark skepticism**: Most popular benchmarks are saturated or contaminated; know which ones still discriminate
3. **Triangulate**: Never rely on a single leaderboard; cross-reference multiple sources
4. **Commercial vs research**: Distinguish what works in research papers from what deploys in production
5. **Cost awareness**: Discovery without cost context is incomplete — every model recommendation needs pricing

---

## 1. FRONTIER MODEL LANDSCAPE (2025-2026)

### Current Rankings — Artificial Analysis Intelligence Index (June 2026)

| Rank | Model | Provider | Index Score | Context |
|------|-------|----------|-------------|---------|
| 1 | Claude Opus 4.8 | Anthropic | 61.4 | 200K, SWE-bench 69.2% |
| 2 | GPT-5.5 | OpenAI | 60.2 | 128K, leads ARC-AGI-2 85% |
| 3 | Gemini 3.1 Pro | Google DeepMind | 57 | 1M context |
| 4 | Grok 4.3 | xAI | 53 | 671B params |

**Claude Opus 4.6 Thinking** was the first model to break 1500 Elo on LMSYS Arena (April 2026).

### Anthropic Claude (Current Lineup)

| Model | Price (In/Out per M) | Cached | Context | Best For |
|-------|---------------------|--------|---------|---------|
| Claude Opus 4.8 | $5/$25 | $0.50 | 200K | Complex reasoning, SWE-bench 69.2%, computer use 84% |
| Claude Sonnet 4.6 | $3/$15 | $0.30 | 200K | Production default, 79.6% SWE-bench, 17% faster than Opus |
| Claude Haiku 4.5 | $1/$5 | $0.10 | 200K | Routing, classification, 97 tokens/second |

All support extended thinking mode for complex multi-step reasoning.

### OpenAI (Current Lineup)

| Model | Key Strength | Notable Benchmark |
|-------|-------------|------------------|
| GPT-5.5 | Creative writing, multimodal | ARC-AGI-2: 85% |
| o3 | Deep reasoning, agentic tool use | AIME 2025: 88.9%; GA April 2025 |
| o4-mini | Cost-efficient reasoning | AIME 2025: 92.7%; 15x cheaper than o1 |
| GPT-4o | Workhorse mid-tier | Widely deployed |

o3 and o4-mini are the first OpenAI reasoning models with native agentic tool use.

### Google Gemini (Current Lineup)

| Model | Context | Speed | Key Strength |
|-------|---------|-------|-------------|
| Gemini 2.5 Pro / 3.x | 1M | — | 94.3% GPQA Diamond (highest reasoning), natively multimodal |
| Gemini 2.5 Flash | 1M | 224.6 tok/s, 0.62s TTFT | Best price-performance; ~20-30% token efficiency gain vs prior Flash |

### xAI Grok

- **Grok 3** (Feb 17, 2025): 671B params, 131K context. Trained on Colossus with 200K H100s. AIME 2025: 93.3%, GPQA: 84.6%, MMMU: 81.7%. Features: DeepSearch (real-time web), Think Mode, Big Brain Mode.
- **Grok 4.3** (April 2026): Updated flagship.

---

## 2. OPEN-WEIGHT MODEL LANDSCAPE

### Decision Framework: API vs Open-Weight

**Use APIs when**: <5M tokens/day; don't want to manage infrastructure; need latest capabilities immediately.
**Use open-weight when**: >5M tokens/day (cost crossover); data sovereignty / compliance; need fine-tuning on proprietary data; privacy requirements.

### Meta Llama 4 (April 2025)

| Model | Total/Active Params | Context | License |
|-------|-------------------|---------|---------|
| Llama 4 Scout | 109B / 17B active (MoE) | 10M tokens | Llama 4 Community |
| Llama 4 Maverick | 400B / 17B active (MoE) | 1M | Llama 4 Community |
| Llama 4 Behemoth | ~2T / 288B active | — | Announced, not released |

Scout's 10M token context window is the largest commercially available context of any open-weight model.

### DeepSeek (Open Weights, MIT License)

**DeepSeek R1** (January 20, 2025): 671B total / 37B active (MoE). Trained via SFT on CoT data then GRPO RL. Benchmarks: AIME ~79.8%, MATH-500 ~97.3%. Performance parity with OpenAI o1 at a fraction of the cost.

**DeepSeek V3.2 Speciale**: $0.28/M input tokens — most cost-efficient frontier-adjacent model. Achieved gold at IMO, CMO, ICPC World Finals, IOI 2025.

### Alibaba Qwen Series

| Model | Key Capability | Benchmark |
|-------|---------------|-----------|
| Qwen 3.5 27B | Coding specialist | 77.2% SWE-bench Verified |
| Qwen 3.6 Plus | 1M context, computer use, agentic coding | — |
| Qwen3.7-Max (May 2026) | Long-horizon agentic missions | — |

Qwen3.5-9B outperforms GPT-OSS-120B on HMMT Feb 2025 competition math (83.2 vs 76.7).

### Mistral AI

| Model | Params | Context | Benchmark |
|-------|--------|---------|-----------|
| Mistral Large 2 | 123B | 128K | 84% MMLU; 76.9% coding |
| Devstral 2 (Dec 2025) | — | 256K | 72.2% SWE-bench Verified |

Mistral Large 2 license: Mistral Research License (open for research, commercial terms separate).

### Other Notable Models

- **MiniMax M2.5**: 80.2% SWE-bench Verified — matches Claude Opus 4.6 on coding
- **Nvidia Nemotron 3 Super 120B-A12B**: Optimized for agentic workflows, 1M context
- **Google Gemma 4**: Competitive open-weight from Google

---

## 3. BENCHMARK EVALUATION METHODOLOGY

### The Saturation and Contamination Crisis

**Do not rely on these benchmarks** — they are saturated, contaminated, or both:
- MMLU: Frontier models score 88-94%. No longer discriminates.
- HumanEval: 164 problems, widely memorized across all frontier models.
- GSM8K: Saturated.

### Current Recommended Benchmarks (2026)

| Benchmark | Domain | Score Range (Frontier) | Why Use |
|-----------|--------|----------------------|---------|
| **GPQA Diamond** | Expert science (PhD-level) | 84-94% | Not saturated; genuine reasoning |
| **SWE-bench Pro** | Real GitHub issues, multi-language | 60-80% | 1,865 tasks, contamination-resistant |
| **LiveCodeBench** | Competitive programming | Varies | New problems monthly — contamination-resistant |
| **ARC-AGI-2** | Abstract reasoning | 55-85% | Zero-shot at launch; novel pattern induction |
| **HLE** (Humanity's Last Exam) | Cross-domain expert questions | ~30-46% | 2,500 questions, 100+ subjects; best not yet solved |
| **AIME 2025** | Competition math | 79-93% | Official, new each year |
| **LMSYS Arena** | Human preference (blind A/B) | Elo scores | No contamination possible; reflects real use |

### Evaluation Frameworks

**EleutherAI LM Evaluation Harness** (github.com/EleutherAI/lm-evaluation-harness): de facto standard. Powers Hugging Face Open LLM Leaderboard. 200+ tasks via config files. Extension requires no code changes.

**HELM** (Stanford CRFM): Holistic evaluation across accuracy, calibration, robustness, fairness, bias, toxicity, efficiency simultaneously. Use when reporting safety alongside capability.

**RAGAS** (for RAG systems): faithfulness, answer_relevancy, context_precision, context_recall.

### How to Read a Leaderboard

1. Classify governance: who runs it? Does the lab submit its own scores?
2. Check contamination risk and saturation level
3. Read the harness/methodology docs
4. Weight recent benchmarks (SWE-bench Pro, LiveCodeBench, HLE) over legacy ones
5. Cross-reference at minimum: Artificial Analysis + LMSYS Arena + one agentic coding leaderboard
6. Use confidence intervals — never rank models without them

### Custom Eval Build Checklist

1. Define task precisely (input format, output format, success criteria)
2. Prevent contamination (use post-training-cutoff sources; hash-check against training data)
3. n ≥ 200 for statistically meaningful ranking
4. Report confidence intervals (bootstrap or Wilson)
5. Report prompt sensitivity (run 3+ prompt variants per task)
6. Triangulate: static benchmark + human-preference arena + agentic task suite

---

## 4. AI RESEARCH DISCOVERY

### Primary Discovery Channels

**arXiv** (arxiv.org): The canonical preprint server. Key sections:
- `cs.AI` — Artificial Intelligence
- `cs.CL` — Computation and Language (NLP/LLMs)
- `cs.LG` — Machine Learning
- `stat.ML` — Statistical Machine Learning
- `cs.CV` — Computer Vision

Daily workflow: arXiv digest emails for chosen sections + Twitter/X monitoring where researchers post paper threads on submission day (typically 6-12 hours ahead of digest).

**Hugging Face Hub** (huggingface.co): Post-Papers With Code acquisition (July 2025), now the single hub for: model cards and weights (500K+ models), datasets (300K+), Spaces (demos), trending papers with linked implementations, Open LLM Leaderboard.

**Semantic Scholar** (semanticscholar.org): Allen Institute. 200M+ papers indexed. Key features: AI-generated TLDRs, citation graphs, alerts when tracked papers get cited, influence scoring, free for all discovery.

**Note**: Papers With Code shut down July 2025 (acquired by Hugging Face). Use CodeSOTA (codesota.com) as a live alternative for SOTA benchmark pages.

### Breakthrough Paper Detection — Fastest Pipeline

1. Follow `@_akhaliq` on X — posts every major arXiv paper with summary; 12-24 hours ahead of newsletters
2. Subscribe to Hugging Face Daily Papers newsletter
3. Monitor `#papers` in key Discords (EleutherAI, HuggingFace)
4. Set Semantic Scholar alerts for high-impact researchers (track Anthropic/OpenAI/Google researcher pages)

---

## 5. AI TOOL ECOSYSTEM

### Coding Assistants (2025-2026)

| Tool | Model | Price | Best For |
|------|-------|-------|---------|
| GitHub Copilot | GPT-4o, Claude, Gemini | $10/mo individual, $19/mo business | IDE integration, 20M+ users |
| Cursor | Claude Sonnet 4.6 default | $20/mo Pro | Multi-file agent edits, Composer; $29.3B valuation (Nov 2025) |
| Windsurf (ex-Codeium) | Cascade | $15/mo Pro | Agentic editing, free tier |
| Claude Code | Claude Opus/Sonnet 4.x | Usage-based | Complex agentic coding, CLI, MCP integration |

### Image Generation (2025-2026)

| Tool | Strength | Current Version | Notes |
|------|---------|----------------|-------|
| Midjourney | Aesthetics, artistic quality | V8 (March 2026) | V7 April 2025, V8 5x faster, native 2K |
| Flux (Black Forest Labs) | Realism, commercial use | Flux 2 (Nov 2025), FLUX.1.1 Pro | 4.5s generation; preferred for photographic/commercial |
| Stable Diffusion | Open source, full customization | 3.5 | Locally runnable |
| GPT Image | Instruction following | GPT Image 2 (April 2026, replaced DALL-E 3) | Complex prompt adherence |

**Midjourney pricing**: Basic $10/mo, Standard $30/mo (unlimited relax), Pro $60/mo, Mega $120/mo.

### Video Generation (2025-2026)

| Tool | Strength | Version |
|------|---------|---------|
| Google Veo | Quality leader, native 4K + audio | Veo 3.1 |
| Kling (Kuaishou) | Best value ($0.07/sec), multi-shot | Kling 3.0 |
| Runway | Creative control, camera moves | Gen-4.5 |
| Seedance | Native audio sync | Seedance 2.0 |

**Key 2026 differentiator**: Native audio generation in one pass (synchronized dialogue, ambient sound, music). Kling 3.0 Omni, Seedance 2.0, and Veo 3.1 all support this.

**OpenAI Sora**: Discontinued in 2026. Generated $2.1M lifetime revenue while burning $15M/day in compute.

### Voice / Speech Generation (2025-2026)

| Tool | Latency | Key Strength | Price |
|------|---------|-------------|-------|
| ElevenLabs (Eleven v3, GA Feb 2026) | 400-600ms TTFT | Best overall quality, 74 languages, emotion tags | $500M Series D at $11B |
| Cartesia (Sonic-3, Oct 2025) | <100ms TTFT | Latency leader — real-time voice agents, phone bots | $100M raised Oct 2025 |
| OpenAI TTS (tts-1, tts-1-hd) | Lowest | Simple narration, 6 voices | API pricing |

**Note**: Meta acquired and shut down Play.ht on December 31, 2025.

---

## 6. AI DATASET DISCOVERY

### Major Open Training Corpora

| Dataset | Size | License | Notes |
|---------|------|---------|-------|
| FineWeb (Hugging Face) | 15T tokens | Apache 2.0 | Best open pretraining corpus; filtered Common Crawl |
| Dolma (AI2) | 3T tokens | Apache 2.0 | Powers OLMo; 24 CC snapshots + GitHub + Semantic Scholar papers |
| Common Crawl | ~3-5B pages/month | Unstructured | Requires heavy filtering (CCNet pipeline, quality classifiers) |
| Common Corpus (2025) | Largest permissively licensed | Permissive | Best for ethical pretraining |
| LAION-5B / Re-LAION-5B | 5.85B image-text pairs | CC-BY | Re-released Aug 2024 after cleanup |

**Hugging Face Dataset Discovery**: huggingface.co/datasets — filter by task, license, downloads. Dataset Viewer for instant in-browser inspection.

### Synthetic Data Generation

**Hugging Face Synthetic Data Generator** (January 2025): no-code tool, 50 samples/minute free tier. Integrates with Argilla for curation.

**DataDreamer** (open-source Python): programmatic synthetic data generation, prompt workflow automation, fine-tuning pipeline integration.

**Quality evaluation checklist for any dataset**:
1. Source diversity (avoid single-domain bias)
2. License review (web scraping doesn't transfer copyright)
3. Deduplication (exact and near-duplicate removal)
4. Toxicity/CSAM screening
5. Language balance for multilingual training
6. Benchmark contamination check (n-gram overlap with eval sets)
7. Annotation agreement scores if human-labeled

---

## 7. AI SAFETY AND ALIGNMENT

### Key Techniques

| Technique | Description | Status |
|-----------|-------------|--------|
| RLHF | Human feedback → reward model → PPO | Baseline; known reward hacking limitations |
| DPO | Direct preference optimization; no separate reward model | Dominant for instruction-following fine-tuning as of 2025 |
| RLAIF | AI-generated preference labels; scales annotation | Widely adopted at frontier labs |
| Constitutional AI 2.0 (Anthropic, Feb 2026) | Dynamic constitution updates; 40% reduction in harmful outputs vs RLHF-only | Combines CAI + DPO + RLHF |

**Mechanistic Interpretability** (MIT Tech Review "10 Breakthrough Technologies 2026"): Anthropic sparse autoencoders identified interpretable features in Claude 3.5 Haiku's activations. Attribution graphs examine internal reasoning processes.

### Key Safety Organizations

| Org | Focus |
|-----|-------|
| Anthropic | Constitutional AI, interpretability, evaluations |
| ARC (Alignment Research Center) | Alignment theory, model evaluations |
| MIRI | AI alignment theory; long-horizon theoretical work |
| Apollo Research | Independent red-teaming, deployed model evaluations |
| MATS | Safety researcher training (120 fellows, Summer 2026) |

### Safety Benchmarks

- **StrongREJECT**: Refusal quality (avoiding over-refusal and harmful outputs)
- **WildGuard / HarmBench**: Red-team attack resistance
- **CyberSecEval** (Meta): Cybersecurity safety evaluations

---

## 8. NEWS AND SIGNAL SOURCES

### Newsletters (Ranked by Signal Quality)

| Newsletter | Audience | Best For |
|-----------|---------|---------|
| Import AI (Jack Clark) | Researchers/policy | Frontier research, safety, policy — weekly |
| Interconnects (Nathan Lambert) | Researchers | RLHF, alignment, frontier lab analysis — weekly |
| Latent Space (swyx + Alessio) | Engineers | AI engineering, agents, production — weekly + daily AINews |
| The Batch (Andrew Ng) | Broad technical | Research + industry — weekly |
| TLDR AI | Technical | Link digest, 1.25M+ subscribers — daily |
| The Rundown AI | General | Landscape scan, 2M+ subscribers — daily |

**Recommended stack**: TLDR AI (5-min daily scan) + Interconnects or Latent Space (20-min weekly synthesis).

### Podcasts

| Podcast | Host | Strength |
|---------|------|---------|
| Lex Fridman Podcast | Lex Fridman | Highest reach, long-form interviews |
| Dwarkesh Podcast | Dwarkesh Patel | Deep technical interviews with AI researchers |
| Latent Space | swyx + Alessio Fanelli | Best developer-focused content |
| No Priors | Elad Gil + Sarah Guo | Founder/executive perspective |

### Essential Twitter/X Accounts

- **@_akhaliq** — ArXiv papers with summaries, same-day coverage
- **@karpathy** (Andrej Karpathy, Anthropic as of May 19, 2026) — Deep learning intuition
- **@ylecun** — Open-source AI, dissenting views on AGI timelines
- **@fchollet** — ARC-AGI creator, rigorous benchmarking critique
- Lab accounts: @AnthropicAI, @OpenAI, @GoogleDeepMind, @xai, @MistralAI, @huggingface

---

## 9. FINE-TUNING AND CUSTOMIZATION

### Decision Framework

| Goal | Best Approach |
|------|--------------|
| Inject new factual knowledge | RAG (faster iteration, always current) |
| Style/tone/format consistency | Fine-tuning |
| Strict domain-specific output schemas | Fine-tuning |
| Reduce latency via smaller model | Fine-tuning (distillation) |
| Privacy / keep data local | Fine-tuning open-source model |
| <5M tokens/day | API (no infrastructure overhead) |
| >5M tokens/day | Evaluate self-hosting |

### Techniques

**LoRA**: Freeze base weights, train low-rank decomposition matrices (rank 4-64). ~95% fewer trainable params. Small adapter files (~50-500MB).

**QLoRA**: LoRA + 4-bit NF4 quantization. Fine-tune 70B models on a single RTX 4090 (24GB VRAM). 80-90% of full fine-tuning quality.

**Full fine-tuning**: 100-120GB VRAM for 7B model. Reserved for maximum performance with budget.

### Fine-Tuning Platforms (2025-2026)

| Platform | Specialty | Notes |
|----------|-----------|-------|
| Together AI | Only major inference provider with built-in fine-tuning API | Usage-based |
| Fireworks AI | Best developer experience for custom model deployment | Usage-based |
| RunPod | Cheapest self-serve GPU for LoRA training | $1.99/hr H100 PCIe |
| Modal | Serverless Python for fine-tuning pipelines, GPU burst | Per-second billing |
| Anyscale | Enterprise distributed training (Ray-based) | Enterprise |
| Databricks | MLOps + fine-tuning in existing data stack | Enterprise |

**⚠️ OpenPipe warning**: OpenPipe training/inference migrating to W&B; legacy platform stops new training July 30, 2026.

---

## 10. GPU INFRASTRUCTURE

### GPU Cloud Pricing (H100, March 2026)

H100 rental rates fell 64-75% between Q4 2024 and early 2026.

| Provider | H100 PCIe | H100 SXM | Best For |
|----------|-----------|---------|---------|
| RunPod | $1.99/hr | $2.69/hr | Cheapest self-serve; serverless tier |
| Lambda Labs | $3.29/hr | $4.29/hr | Research-friendly workflow |
| CoreWeave | $4.25/hr | Higher | Enterprise SLA, availability guarantees |
| Vast.ai | <$1.60/hr | N/A | P2P marketplace; lowest cost, variable reliability |
| B200 (Blackwell via CoreWeave) | ~$2.12/hr | — | Next-gen for large runs |

**Strategy**: Train on Lambda or CoreWeave (sustained runs with SLA); serve on RunPod serverless or Vast.ai (variable load).

### Inference API Providers (Open-Weight Models)

| Provider | Speed | Breadth | Fine-tuning | Best For |
|----------|-------|---------|------------|---------|
| Groq | Fastest: 456 TPS, <300ms TTFT (LPU hardware) | ~20 curated models | No | Real-time chat, streaming |
| Fireworks AI | 747 TPS, 0.17s latency | Wide + custom | Yes | Dev experience |
| Together AI | 917 TPS, 0.78s latency | 100+ models | Yes (built-in) | Price leader at scale |
| Cerebras | Ultra-fast for specific architectures | Limited | No | Latency-critical |

**Groq**: Custom Language Processing Units (LPUs) instead of GPUs — 2.6x faster end-to-end throughput than Fireworks/Together on identical models, consistent TTFT <300ms. Smaller but curated catalog.

**Together AI**: Most complete platform — 100+ models, built-in fine-tuning API, best per-token pricing at production scale.

**Llama 3.3 70B pricing comparison**: Groq $0.59/$0.79, Fireworks $0.90 flat, Together $1.04/$1.04 per million tokens.

---

## KEY DISCOVERY URLs

| Resource | URL |
|----------|-----|
| Artificial Analysis Intelligence Index | artificialanalysis.ai/models |
| LMSYS Chatbot Arena | chat.lmsys.org |
| LLM Stats (300+ models) | llm-stats.com |
| Hugging Face Hub | huggingface.co |
| Open LLM Leaderboard | huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard |
| arXiv cs.CL | arxiv.org/list/cs.CL/recent |
| Semantic Scholar | semanticscholar.org |
| CodeSOTA (SOTA tracker) | codesota.com |
| ARC Prize / ARC-AGI-2 | arcprize.org |
| HLE Leaderboard | labs.scale.com/leaderboard/humanitys_last_exam |
| SWE-bench Pro | swebench.com |
| EleutherAI Eval Harness | github.com/EleutherAI/lm-evaluation-harness |
| GPU price comparison (64 providers) | getdeploying.com/gpus |
| Groq | groq.com |
| Together AI | together.ai |
| Fireworks AI | fireworks.ai |
