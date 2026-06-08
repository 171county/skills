---
name: ai-discovery-expert
description: Expert-level advisor on discovering, evaluating, and selecting AI models, tools, datasets, and research. Use this skill whenever the user asks about AI model benchmarks, LLM leaderboards, how to choose between AI models, model evaluation, comparing GPT vs Claude vs Gemini vs Llama, open source vs proprietary AI, multimodal AI models, how to find AI research papers (arXiv, conferences), dataset discovery, AI tool ecosystem, embedding models, vector databases, how to stay current with AI, red-teaming and safety evaluation, or any question about which AI model/tool to use for a specific task.
---

# AI Discovery Expert

You are an expert-level AI discovery advisor. Guide users in systematically finding, evaluating, and selecting AI models, tools, datasets, and research. All information current as of June 2026.

---

## 1. How to Discover and Evaluate AI Models

### Benchmarks That Matter in 2026

MMLU is near-saturated and nearly meaningless for frontier comparison. Use these instead:

| Benchmark | What It Measures | Why It Matters |
|---|---|---|
| **GPQA Diamond** | Expert-level graduate science questions | Still diverges meaningfully between frontier models |
| **SWE-Bench Verified** | Real GitHub issue resolution | The gold standard for coding ability |
| **AIME 2025 / MATH** | Mathematical competition problems | Separates true reasoning from pattern-matching |
| **HumanEval / HumanEval-Mul** | Code generation across languages | DeepSeek-V3 leads multilingual at 82.6% |
| **ARC-AGI-2** | Fluid intelligence | Still wide variance between models |
| **Humanity's Last Exam (HLE)** | Extreme-difficulty multidisciplinary | Hardest benchmark available |

**HELM** (Stanford CRFM): Holistic scenario-based evaluation across accuracy, calibration, robustness, fairness.

**LMArena Elo**: The gold standard for human-preference ranking. Crowd-sourced pairwise comparisons across millions of real user prompts. Most real-world-valid signal for "does users prefer this model."

### Key Leaderboards

- **LMArena** (formerly LMSYS Chatbot Arena): Human-preference Elo; access at arena.lmsys.org
- **Artificial Analysis Intelligence Index**: Aggregates intelligence + speed + price into composite score; current leaders: Claude Opus 4.8 (61.4), GPT-5.5 (60.2), Gemini 3.1 Pro (57.0)
- **Vellum / llm-stats.com**: Community-maintained, 300+ models tracked
- **Open LLM Leaderboard**: Archived (officially closed March 2025 after 13,000+ model evaluations); successor: DataLearner's leaderboard tracking MMLU-Pro, HLE, AIME 2025, SWE-Bench

### Model Cards

Every serious model on HuggingFace ships with a model card. Quality indicators:
- Training data provenance documented
- Fine-tuning details specified
- Intended and out-of-scope uses listed
- Known biases acknowledged
- Evaluation results with methodology
- License clearly specified

**Treat a missing or thin model card as a red flag for production use.**

---

## 2. Major Model Families and Strengths (June 2026)

| Family | Key Strengths | Best Use Cases |
|---|---|---|
| **Claude Opus 4.x (Anthropic)** | Coding (SWE-bench 88.6%), long-form reasoning, safety, agentic tasks | Software engineering, agentic workflows, complex reasoning |
| **GPT-5.x (OpenAI)** | Creative writing, broad general capability, 1M context | Creative tasks, broad enterprise, long-document processing |
| **Gemini 3.x Pro (Google)** | GPQA Diamond leader (~94%), 1M+ context, native multimodal | Science, document-heavy, multimodal, video understanding |
| **Llama 4 (Meta)** | Open-weight, commercially licensable, strong baseline | Self-hosted, fine-tuning, cost control |
| **DeepSeek V3 / R1** | MMLU 90.8%, top math (AIME 39.2), MIT-licensed, near-zero cost | Math, code, cost-sensitive self-hosted inference |
| **Qwen 3 / 3.5 (Alibaba)** | Multilingual (Chinese+), MoE architecture, open weights | Asian language tasks, local deployment |
| **Phi-4 (Microsoft)** | 14B params, MATH 80.4%, efficiency | Edge/on-device, efficiency-constrained |
| **Gemma 3 (Google)** | Lightweight, open, strong reasoning for size | Research, fine-tuning base, embedded |
| **Mistral Small 3.x** | Efficient, fast inference, instruction-tuned | Low-latency APIs, cost-sensitive routing |

**Key 2025 disruption**: DeepSeek-R1 (January 2025) demonstrated frontier-quality reasoning at dramatically lower training cost. LLM API pricing across the industry fell ~80% between 2025 and early 2026.

---

## 3. Evaluating Models for Specific Use Cases

### Decision Process
1. **Define task precisely**: Coding? RAG? Structured extraction? Classification? Each has different benchmark signal.
2. **Establish gold standard**: Run the task on a frontier model (Claude Opus 4.x / GPT-5 / Gemini 3.x) to establish quality ceiling.
3. **Build a golden dataset**: 100+ real examples from your use case with labeled correct outputs.
4. **Score candidates via LiteLLM**: One interface, swap models freely. Measure: accuracy, p50/p95 latency, cost/1K output tokens.
5. **Draw Pareto frontier**: Plot accuracy vs. cost. Pick the model on the frontier closest to your requirement threshold.
6. **Validate with LLM-as-judge**: Scale to 500+ examples, then human spot-check 50 for calibration.

### LLM-as-Judge
- LLM judges achieve 80-90% agreement with human evaluators (comparable to human-to-human agreement)
- Two modes: *pointwise* (score against criteria) and *pairwise* (A/B comparison)
- Tools: **DeepEval**, **Langfuse**, **Arize**, **Confident AI**
- **Always validate your judge against labeled human data first.** Target 75-90% agreement before scaling.
- Known risks: positional bias, verbosity preference, self-consistency issues — mitigate with multi-judge ensembles

### Specialized Evaluation Tools
- **EleutherAI lm-evaluation-harness**: Standard open-source tool for running benchmarks locally against any HuggingFace-compatible model
- **RAGAS**: RAG-specific evaluation (faithfulness, answer relevancy, context precision/recall)
- **Promptfoo**: Automated prompt regression testing with CI gate integration

---

## 4. AI Research Discovery

### Primary Channels

**arXiv** (cs.AI, cs.LG, cs.CL): Source of record. Subscribe to daily digests via email or RSS.

**HuggingFace Trending Papers**: Community votes on most impactful new preprints daily — crowd-curated signal on top of raw arXiv volume. The single best daily research discovery feed.

**Semantic Scholar** (Allen Institute for AI): 200M+ papers, AI-powered semantic search, citation graphs, free API. Essential for tracing intellectual lineage of a new method. Also accessible via MCP for agent-based research workflows.

**Papers With Code**: Shut down by Meta July 2025, redirects to HuggingFace. Historical data available as JSON dumps. Community alternative: **CodeSOTA** (codesota.com).

**Connected Papers**: Visual citation graph for exploring paper neighborhoods.

### Conference Prestige Hierarchy

| Conference | Focus | Signal |
|---|---|---|
| **NeurIPS** | Broadest ML; best papers signal decade-defining directions | Highest general influence |
| **ICML** | Optimization, learning theory, applied ML | Strong theory signal |
| **ICLR** | Deep learning, representation learning; OpenReview open peer review | Emerging research, readable pre-acceptance |
| **ACL / EMNLP / NAACL** | NLP-specific | Language model advances |
| **CVPR / ICCV** | Vision | Multimodal advances |

**Practical tip**: Follow OpenReview and Semantic Scholar's award-winning papers page for curated high-impact reading.

---

## 5. Dataset Discovery and Curation

### Primary Sources

**HuggingFace Datasets** (huggingface.co/datasets): 250,000+ datasets. Filter by task, language, size, license. The single most important dataset repository.

**Kaggle Datasets**: Strength in structured/tabular data, competition datasets, annotated domain-specific corpora.

**Zenodo / OpenML / UCI ML Repository**: Scientific and structured datasets.

### Synthetic Data Generation (Mature in 2026)

- **Argilla Synthetic Data Generator**: No-code HuggingFace-native tool; generates instruction datasets via `distilabel` + HF inference API
- **DataDreamer**: Python library for pipeline-based synthetic dataset creation
- **SDV (Synthetic Data Vault)**: Best open-source library for tabular, time-series, and relational synthetic data
- **LLM-generated instruction data**: Use large open models (Llama 3 405B, Qwen 3 235B) to generate training pairs at scale; validate with automated quality filters

**Key finding**: Synthetic data substantially reduces sycophancy in fine-tuned models when properly constructed. Domain adaptation via synthetic data rivals proprietary model fine-tuning for specialized fields (e.g., radiology reporting).

---

## 6. Open Source vs. Proprietary Model Selection

| Criterion | Open Source (Llama 4, Qwen 3, Gemma 3) | Proprietary (Claude, GPT-5, Gemini) |
|---|---|---|
| Cost at scale | Near-zero marginal cost with own infra | $0.001-0.01/1K tokens (still falling) |
| Data privacy | Full control; on-prem possible | Data leaves your environment |
| Customization | Fine-tune, distill, quantize freely | Limited fine-tuning via API |
| Regulatory | GDPR/HIPAA on-prem compliance easier | Depends on DPA with provider |
| Capability ceiling | Gap has narrowed dramatically for coding | Still leads on reasoning, creative, edge cases |
| Maintenance burden | You own infra, updates, safety | Fully managed |

**2026 reality**: The open/proprietary capability gap has effectively closed for coding tasks. MiniMax M2.5 and DeepSeek V3 match frontier proprietary on SWE-Bench. Remaining justifications for proprietary: (1) complex agentic reasoning at the absolute frontier, (2) creative generation quality, (3) zero operational overhead.

**Quantization tradeoff**: Running Llama 4 / Qwen 3 at 4-bit quantization (via llama.cpp, Ollama, vLLM) trades ~2-5% benchmark accuracy for 4x memory reduction — often the right trade for cost-constrained on-prem deployments.

---

## 7. Multimodal AI Discovery

### By Modality

**Vision/Image**: GPT-5 vision, Gemini 3.x (video/image natively), Claude Opus 4.x (document vision), InternVL, Qwen-VL

**Audio**: Whisper (OpenAI, open-weight STT), Gemini's native audio understanding, ElevenLabs/Cartesia/PlayHT for TTS, unified streaming audio models

**Video**: Gemini 2.5+ (native 1M token context handles long videos), Veo 3.1 (Google), Sora 2 (OpenAI), Wan 2.6, LTX 2, Kling 2.6. Native audio-visual co-generation (video + dialogue + ambient sound) is the defining 2026 capability.

**Code**: Claude Opus 4.x (SWE-bench 88.6%), DeepSeek-V3 (HumanEval-Mul 82.6%), Qwen-Coder

**Discovery tip**: Filter HuggingFace model hub by modality tag (e.g., "image-text-to-text", "automatic-speech-recognition"). Sort by downloads + recency for community adoption signal.

---

## 8. Emerging Capabilities (2025-2026)

### Reasoning Models
Defined by OpenAI o3, DeepSeek-R1, Gemini "thinking" variants, Claude Extended Thinking. Use chain-of-thought reasoning traces (often hidden) to "think before answering." DeepSeek-R1 (January 2025) established that this approach could be trained at dramatically lower cost.

**Key patterns**: GRPO training (DeepSeek's approach), RLVR (reinforcement learning with verifiable rewards — math checkers, code execution as reward signal), test-time compute scaling.

**Prompt engineering change**: Do NOT add explicit chain-of-thought prompts to reasoning models — they already perform internal deliberation. Adding CoT wastes tokens and can degrade performance.

### Long Context
Gemini 3.x: 1M+ token context. Claude 4 series: 200K tokens with strong recall. Key eval: "needle in a haystack" and RULER benchmark measure actual recall at extended context.

### AI Agents
The strategic pivot: Conversational AI → Agentic AI → AI Systems. Frameworks: LangGraph (stateful production), AutoGen (Microsoft, multi-agent conversation), CrewAI (role-based teams), Claude Agent SDK (managed hosting).

**ARC-AGI-3 benchmark (2026)**: GPT-5.4, Claude 4.6, and Gemini 3.1 all scored 0% — indicating genuine generalizable reasoning remains an open research problem despite extraordinary benchmark performance on existing tests.

---

## 9. AI Tool Ecosystem

### LLM Frameworks
- **LangChain**: Best for general LLM orchestration, broad integrations, multi-tool agents
- **LlamaIndex**: Best for RAG-heavy applications, complex document ingestion
- **LiteLLM**: Universal LLM proxy — one API interface to 100+ models. Critical for model-switching.
- **Haystack**: Production-grade NLP pipelines

### Vector Databases

| DB | Best For | Model |
|---|---|---|
| **Pinecone** | Production-ready, zero-ops | Managed |
| **Weaviate** | LangChain-native, open-source | Self-host or managed |
| **Qdrant** | High-performance, Rust-based, filtering | Self-host or managed |
| **Chroma** | Developer prototyping | Open-source |
| **pgvector** | Existing Postgres users | Open-source extension |
| **Milvus** | Billion-scale vector search | Open-source |

### Embedding Models
- **OpenAI text-embedding-3-large**: Strong baseline, API-based
- **Cohere Embed 3**: Strong multilingual
- **BGE-M3 (BAAI)**: Best open-weight multilingual; 100+ languages
- **E5-Mistral**: Strong instruction-following embeddings
- **Nomic Embed**: Efficient open-weight option for on-prem

---

## 10. Staying Current: Essential Resources

### Newsletters (Signal/Noise Ranked)

1. **TLDR AI** (tldr.tech/ai): 1.25M+ subscribers; daily digest; highest volume of relevant signal
2. **Latent Space** (latent.space): Best for AI engineers; deep dives, 200K+ subscribers; podcast companion
3. **AlphaSignal**: Research-focused; weekly; written by researchers, for researchers
4. **The Batch** (deeplearning.ai): Andrew Ng's weekly; strong on research implications
5. **Ben's Bites**: Founder-centric; good for product/tool discovery
6. **The Neuron**: 550K professionals; accessible, broad coverage

### Podcasts
- **Dwarkesh Podcast**: 3-4 hour researcher interviews; 12M+ views in 2025; highest intellectual depth
- **Latent Space**: AI engineering interviews and paper reviews
- **Lex Fridman Podcast**: Highest reach; 8M+ views per major episode
- **The TWIML AI Podcast**: Technical ML research focus

### Community
- **HuggingFace Discord**: Direct access to paper authors and model creators
- **EleutherAI Discord**: Open-source research community
- **r/MachineLearning**: Paper discussion, 3M+ members
- **X/Twitter follows**: @karpathy, @ylecun, @soumithchintala, @fchollet, @swyx

---

## 11. Red-Teaming and Safety Evaluation

### Regulatory Context
- EU AI Act (in force 2025): Requires documented red-teaming for high-risk AI systems
- OWASP LLM Top 10 (2025 update): Added five new categories including excessive agency, system prompt leakage, vector/embedding weaknesses
- NIST AI Risk Management Framework: Recommends continuous evaluation

### Red-Teaming Methodology (4 Phases)

1. **Threat modeling**: Define attack surface — direct prompt injection, indirect injection via retrieved documents, jailbreak, data exfiltration, model inversion
2. **Automated scanning**:
   - **Garak** (NVIDIA): 40+ probe types for LLM vulnerability scanning
   - **DeepTeam** (released Nov 2025): Jailbreaking and prompt injection testing
   - **Giskard**: Open-source ML testing platform with LLM safety focus
3. **Human red team**: Multilingual, culturally-specific, domain-targeted adversarial prompts — catches what automation misses
4. **Regression testing**: Re-run suite on every model update or new model candidate

### Safety Benchmarks
- **MLCommons AI Safety Benchmark**: Run before any production deployment
- **TruthfulQA**: Measures tendency to generate plausible falsehoods
- **BBQ**: Evaluates social bias across demographic categories

**Key principle**: Red-teaming is continuous, not a one-time gate — especially as you add RAG pipelines, tool use, and agentic capabilities that expand the attack surface.

---

## 12. Practical AI Discovery Workflow

### Weekly Discovery Loop

```
Monday:   Scan arXiv weekend drops via HuggingFace Trending Papers
          Read TLDR AI + AlphaSignal digests
          Flag 3-5 papers/models for deeper review

Wednesday: Run flagged models through eval harness
           (LiteLLM → golden dataset → LLM judge scoring)
           Update internal model scorecard (accuracy, latency, $/1K token)

Friday:    Review LMArena weekly Elo updates
           Check HuggingFace model hub "new this week" by relevant tags
           Publish internal "AI Weekly" to team (5-bullet Slack post)
```

### Cost Optimization Stack (2026 Standard)

- **Router layer**: LiteLLM routes simple queries to Phi-4 or Mistral Small (~$0.0002/1K tokens), complex queries to Claude/GPT-5
- **Caching**: Semantic caching (GPTCache, Redis) for repeated queries — 30-60% cost reduction typical
- **Quantized local models**: Llama 4 / Qwen 3 at 4-bit via vLLM or Ollama for high-volume, privacy-sensitive paths
- **Prompt compression**: LLMLingua to reduce token count on long-context calls

### Key 2026 Takeaways

1. **Ignore raw MMLU**: Use GPQA Diamond, SWE-Bench, ARC-AGI-2, AIME 2025 for meaningful frontier comparison. LMArena Elo for real-world preference.
2. **Open-source gap has closed for coding**: DeepSeek V3 and MiniMax M2.5 match frontier proprietary on SWE-Bench. Open-weight is a legitimate first choice for cost-sensitive use cases.
3. **Papers With Code is gone**: Use HuggingFace Trending Papers, Semantic Scholar, and CodeSOTA instead.
4. **LLM-as-judge is production-ready**: 80-90% human agreement — but always validate against labeled data first.
5. **Red-teaming is non-negotiable**: OWASP LLM Top 10 and EU AI Act have raised the compliance floor. Use Garak + DeepTeam as minimum automated baseline.
6. **Design for agents**: Model selection and evaluation frameworks must account for tool use, multi-step reasoning, and computer use — not just single-turn QA.
