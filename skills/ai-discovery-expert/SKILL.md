---
name: ai-discovery-expert
description: Expert-level AI discovery advisor covering systematic landscape scanning, benchmark evaluation, foundation model selection, evaluation frameworks, AI capability mapping, emerging modalities, agent capability assessment, research discovery methodology, dataset curation, fine-tuning vs RAG vs prompt engineering decision frameworks, open-source vs proprietary tradeoffs, on-premises vs cloud inference, hardware landscape, competitive AI product analysis, and building internal AI capability assessment processes. Provides authoritative guidance on navigating the rapidly-evolving AI ecosystem as of 2025-2026.
---

# AI Discovery Expert

## Overview and Expert Mandate

The AI landscape transforms every 90 days. A model that was frontier-class six months ago is now a mid-tier option. Benchmarks saturate within 12 months of release. New modalities, architectures, and deployment patterns emerge quarterly. The AI Discovery Expert's role is to systematically navigate this landscape — identifying what's real, what's hype, what's applicable, and what's coming — and translate that intelligence into actionable capability decisions for organizations.

---

## 1. Systematic AI Landscape Scanning

### Model Registry Ecosystem

Monitor all major registries systematically:

| Registry | Scope | Key Signal |
|---------|-------|-----------|
| Hugging Face Hub | 2M+ models, 500K+ datasets | Trending models, download counts, community adoption |
| OpenAI Platform | GPT-5 family, embeddings, audio, image | Capability changelog, deprecation notices |
| Anthropic API | Claude Opus/Sonnet/Haiku families | Context window, tool use, extended thinking |
| Google AI / Vertex AI | Gemini 2.5 family, Veo, Imagen | Multimodal capabilities, long context |
| Replicate | Open-source models as APIs | Community models, specialized fine-tunes |
| AWS Bedrock | Multi-provider (Anthropic, Meta, Mistral) | Enterprise managed inference |
| Azure AI Foundry | OpenAI + open models | Enterprise compliance, regional deployment |

**Expert practice:** Automate weekly registry sweeps using the `huggingface_hub` Python client, filtering by `trending`, `downloads > 10K`, and `last_modified > 7 days`. Set up RSS alerts on model cards for flagship models you track.

### Scanning Cadence

| Frequency | Activity |
|-----------|----------|
| Daily | LMArena Elo rankings, llm-stats.com composite rankings |
| Weekly | Hugging Face Open LLM Leaderboard, MTEB (embeddings), BigCode leaderboard |
| Monthly | HELM specialty scenarios, CodeSOTA coding benchmarks, Anthropic/OpenAI/Google release notes |
| Quarterly | Deep capability assessment of top-tier models for your specific use cases |

### Signal Sources

**Primary signals:**
- Benchmark performance (with contamination awareness)
- Arena Elo (cannot be gamed — blind preference voting)
- SWE-bench performance (for engineering use cases)
- Model card documentation quality
- API documentation and tooling maturity

**Secondary signals:**
- Twitter/X discourse from AI researchers (Geoffrey Hinton, Andrej Karpathy, François Chollet, Yann LeCun)
- arXiv preprints (cs.AI, cs.LG, cs.CL) — daily alert for key author names and topics
- Conference proceedings: NeurIPS, ICML, ICLR, ACL, EMNLP
- Hugging Face blog, Anthropic research, OpenAI research, Google DeepMind blog

---

## 2. Benchmark Ecosystem: What to Trust

### General Language Understanding

**MMLU (Massive Multitask Language Understanding):**
- 57 tasks spanning STEM, humanities, social sciences
- **Status 2026: Saturated.** All frontier models score above 88%. Do not use as primary signal for model selection.
- Still useful for: establishing baseline capability of smaller/cheaper models

**GPQA Diamond (Graduate-Level Google-Proof Q&A):**
- PhD-level questions in biology, chemistry, physics
- **Current gold standard reasoning signal** — produces 15+ point spreads between frontier models
- As of mid-2026: Claude Mythos Preview 94.6%, Gemini 3.1 Pro 94.3%, Claude Opus 4.7 94.2%, GPT-5.5 93.6%
- Designed to resist web lookup and training data contamination

**HLE (Humanity's Last Exam):**
- 3,000 questions contributed by domain experts
- Human expert baseline ~90%; best AI (GPT-5.5 Pro with tools) ~57.2%
- Most honest single number for "how far is AI from human expertise at the frontier"

### Coding Benchmarks

**HumanEval:** Status 2026: Saturated. Should not be used for frontier model selection.

**SWE-bench Verified:**
- Real GitHub issue resolution on verified, unambiguous problems
- **Dominant signal for software engineering capability**
- As of June 2026: Claude Opus 4.7 87.6%, Grok 4 ~75%, GPT-5.4 ~74.9%
- Requires multi-file context understanding, debugging, test-running — the full engineering loop

**LiveCodeBench:**
- Continuously sources fresh competitive programming problems post-training cutoff
- **Most contamination-resistant coding benchmark** — problems are always new
- Strongly preferred over HumanEval for current model comparison

**BigCodeBench:**
- 1,140 tasks requiring library calls, APIs, practical programming skills
- Better proxy for real developer work than algorithm puzzles

### Human Preference Evaluation

**LMSYS Chatbot Arena / LMArena:**
- 1M+ blind A/B battles, user-voted preference
- **Gold standard for holistic chat quality** — cannot be gamed by training on benchmark questions
- Elo scores updated continuously; significant ranking changes are meaningful
- The one benchmark where marketing claims cannot inflate scores

### Benchmark Red Flags

**Contamination:** If a model was trained on data that includes benchmark questions, scores are inflated. Signs: rapid benchmark improvement without general capability improvement; model reproduces exact problem text when asked.

**Saturation:** When top models cluster within noise threshold (<2 points apart), a benchmark no longer differentiates. Switch to harder variants.

**Task mismatch:** A benchmark that tests abstract reasoning may not predict performance on your specific use case (customer support, code review, document analysis).

**Expert rule:** Never make a model selection decision based on a single benchmark. Use 3+ benchmarks across relevant dimensions, plus your own task-specific evaluation.

---

## 3. Foundation Model Evaluation for Specific Use Cases

### The Seven-Dimensional Evaluation Framework

| Dimension | What to Measure | How to Measure |
|-----------|----------------|----------------|
| Task accuracy | Correctness on representative inputs | Custom eval set (50–200 examples) |
| Instruction following | Does model do exactly what requested? | Structured prompt tests |
| Output format consistency | JSON/structured output reliability | 100-call test; count malformed outputs |
| Context utilization | Can model use long context effectively? | Needle-in-haystack tests |
| Hallucination rate | Factual errors on grounded tasks | Test with verifiable facts |
| Latency | P50/P95 response time | Load testing at expected QPS |
| Cost | $/1K outputs at production volume | Cost calculator with actual token counts |

### Building a Task-Specific Eval Set

Step 1: Collect 50-200 real examples from your use case (production logs, customer conversations, representative documents).

Step 2: Create ground truth answers for each example. Ground truth should be created by humans or by a reference model that you trust for this specific task.

Step 3: Define an evaluation function. Options:
- **Exact match:** For classification, entity extraction, SQL generation
- **Contains match:** For factual recall
- **LLM-as-judge:** For quality, tone, helpfulness (use GPT-5 or Claude Opus as judge with rubric)
- **Human rating:** For subjective tasks where automated eval is unreliable

Step 4: Run all candidate models against your eval set. Compare on accuracy, cost, latency. Create a Pareto frontier plot (accuracy vs. cost) to identify the optimal model for your requirements.

Step 5: Run your eval before and after every significant prompt change or model upgrade.

### Evaluation Platforms

| Platform | Best For |
|----------|---------|
| LangSmith | LangChain ecosystem; comprehensive tracing + eval |
| Braintrust | Startup-friendly; great dataset management; supports all LLMs |
| Weights & Biases (Weave) | Teams already using W&B for ML; LLM eval extension |
| PromptFoo | Open-source; YAML-based config; CI/CD integration |
| Evidently AI | Open-source; data drift + LLM quality |
| RAGAS | Specialized for RAG evaluation (faithfulness, context recall) |

---

## 4. Model Selection Criteria

### Selection Framework by Use Case

| Use Case | Priority | Recommended Model Tier | Cost Sensitivity |
|----------|----------|----------------------|-----------------|
| Customer support automation | Accuracy + cost | Mid-tier (Claude Haiku, GPT-4o mini) | High |
| Complex reasoning / analysis | Accuracy | Top-tier (Claude Opus, GPT-5, Gemini 2.5 Pro) | Low |
| Code generation (complex) | SWE-bench performance | Claude Opus 4.7 or GPT-5.4 | Medium |
| Document processing at scale | Cost + accuracy | Mid-tier with caching | High |
| Real-time conversation | Latency + cost | Fast tier (Claude Haiku, GPT-4o mini, Gemini Flash) | High |
| Long document analysis | Context window | Gemini 2.5 Pro (2M ctx), Claude (200K ctx) | Medium |
| Embeddings | Accuracy + cost | text-embedding-3-large or Cohere Embed v4 | High |

### The Build-Measure-Learn Loop for Model Selection

**Do NOT:** Run one benchmark, pick a model, commit.

**Do:** Start with the 2-3 models that appear most promising on public benchmarks → build custom eval → test all three → pick the Pareto-optimal model → re-evaluate every 6 months as new models release.

### Cost-Quality Tradeoffs (2025-2026 Reference Prices)

| Model | Input $/1M | Output $/1M | Context | Quality Tier |
|-------|-----------|------------|---------|-------------|
| GPT-5.5 | $5.00 | $30.00 | 128K | Frontier |
| Claude Opus 4.7 | $5.00 | $25.00 | 200K | Frontier |
| Gemini 2.5 Pro | $1.25 | Variable | 2M | Frontier |
| Grok 4.1 Fast | $0.20 | $0.50 | 2M | Near-frontier |
| Claude Haiku 4.5 | $1.00 | $5.00 | 200K | Fast/efficient |
| Gemini 2.5 Flash | $0.30 | Variable | 1M | Fast/efficient |
| DeepSeek V3.2 | $0.14 | Variable | 128K | Open-weights budget |

---

## 5. AI Capability Mapping

### Capability Taxonomy

**Natural Language Processing:**
- Text generation (instruction following, creative writing, summarization)
- Classification (intent detection, sentiment, categorization)
- Entity extraction (structured data from unstructured text)
- Question answering (closed-book and RAG-augmented)
- Translation (99+ languages)
- Code generation and understanding

**Reasoning:**
- Mathematical reasoning (AIME-level)
- Scientific reasoning (GPQA Diamond-level)
- Multi-step logical reasoning
- Causal reasoning
- Analogical reasoning

**Multimodal:**
- Image understanding (describe, classify, extract data from)
- Document understanding (PDF, charts, tables, forms)
- Video analysis (temporal understanding, scene description)
- Audio transcription and understanding
- Computer vision for UI/screenshot interpretation
- Generated image quality evaluation

**Agentic:**
- Tool use / function calling
- Multi-step task completion
- Web browsing (navigation, extraction, interaction)
- Code execution (write code + run + debug)
- Computer use (visual UI interaction — mouse/keyboard)

### 2025-2026 Frontier Capabilities

**Frontier model capability state (June 2026):**

- **Long context:** Gemini 2.5 Pro at 2M tokens handles full codebases; Claude at 200K handles full legal documents
- **Computer use:** Claude's computer use API (stable), GPT-4o with vision — control desktop/browser UIs via screenshots
- **Code execution:** All top-tier models can write code AND execute it in sandboxed environments
- **Real-time audio:** GPT-4o realtime API enables sub-500ms audio conversation; used in voice assistants
- **Video generation:** Sora 2.0 (OpenAI), Veo 3 (Google), Runway Gen 4 — 1080p, 60-second video clips from text/image
- **Extended thinking:** Claude's extended thinking, GPT o3, Gemini Thinking — "slow thinking" modes for complex reasoning

---

## 6. Emerging Modalities (2025-2026)

### Video Understanding and Generation

**Video Understanding (analysis of existing video):**
- Gemini 2.5 Pro: Accepts video input directly; summarizes, Q&A over video content
- Use cases: Meeting summarization, training video analysis, security footage review, sports analytics

**Video Generation (creating video from prompts):**
- Sora 2.0, Veo 3, Runway Gen 4, Kling 1.5 — all produce high-quality 1080p video
- Use cases: Marketing content, product demos, synthetic training data
- Limitation: Temporal consistency and physics simulation still imperfect for complex scenes

### Speech and Audio

**Speech-to-Text:**
- Whisper (OpenAI): State-of-art open-source ASR; word-level timestamps; multilingual
- Deepgram Nova 3: 4-second latency, 90+ languages, $0.0059/min (most cost-competitive)
- AssemblyAI Universal 2: Summarization + entity detection baked in

**Text-to-Speech:**
- ElevenLabs: Gold standard for emotional range and voice cloning; $0.015/1K chars
- OpenAI TTS: Good quality, fast, simple; $0.015/1K chars
- Cartesia Sonic: Ultra-low latency (<80ms) for real-time applications

**Audio Understanding:**
- Gemini accepts audio directly for understanding, summarization, Q&A
- Whisper transcription → LLM analysis (standard pipeline for meeting notes, podcast analysis)

### Image Generation

- **DALL-E 3** (OpenAI): Best prompt adherence; in-platform and API
- **Midjourney v7**: Best aesthetic quality for marketing/brand use
- **Stable Diffusion 4** (Stability AI): Best open-source option; full local deployment
- **Imagen 4** (Google): Integrated with Google Workspace; strong text-in-image
- **Flux.1**: Open-source strong alternative for custom fine-tuning

### 3D and Spatial

- **Point cloud to 3D mesh** pipelines maturing for product design and robotics
- **NeRF and Gaussian Splatting** for scene reconstruction from photos
- **3D generation from text** (Shap-E, Point-E) — still not production-quality as of mid-2026

---

## 7. AI Agent Capability Assessment

### Evaluating Agent Systems

Agents have unique failure modes that standard model benchmarks don't capture:

| Failure Mode | How to Test |
|-------------|------------|
| Tool selection errors | Give agent overlapping tools; verify correct selection |
| Context window exhaustion | Long multi-step tasks; does performance degrade? |
| Loop termination failure | Does agent know when task is complete? |
| Error propagation | Inject a tool error mid-task; does agent recover or fail silently? |
| Hallucinated tool calls | Does agent try to use tools that don't exist? |

### Agent Benchmark Suite (2025-2026)

**WebArena / WebArena-lite:**
- 812 tasks across real websites (shopping, reddit, gitlab, etc.)
- Measures complete task success, not just intermediate steps
- State-of-art: ~55% (human ~88%)

**SWE-bench Verified:**
- Real-world GitHub issue resolution
- Best measure of software engineering agent capability
- As of June 2026: Claude Opus 4.7 ~87.6% (with Claude Code harness)

**GAIA:**
- Real-world assistant tasks requiring web search, file reading, tool use
- Tests multi-step reasoning with external tools

**OSWorld:**
- Computer use tasks on actual OS environments
- GUI navigation, application control, file management

### Building Your Own Agent Evaluation

For production agent systems, build custom evals:

1. **Task library:** 50-200 representative real tasks from your domain
2. **Success criteria:** Binary (task complete/incomplete) + quality rating
3. **Trace analysis:** Log all tool calls, intermediate states, final outputs
4. **Regression suite:** Any bug fixed should add a test case

---

## 8. Staying Current: Research Discovery Methodology

### The arXiv Intelligence Pipeline

**Daily arXiv monitoring:**
- Subscribe to cs.AI, cs.LG, cs.CL (computational linguistics), cs.CV (computer vision)
- Use semantic search: `arxiv-sanity-lite`, `paperswithcode.com`, `arxiv.org/search`
- Alert services: ArxivDigest, PaperTrail, Research Rabbit

**Filtering for signal over noise:**
1. Institution filter: Papers from Anthropic, DeepMind, OpenAI, Meta AI, Stanford, MIT, CMU, ETH Zurich
2. Author filter: Track 10-15 researchers whose work consistently matters to your domain
3. Citation velocity: Papers gaining 100+ citations within 30 days are likely significant
4. Implementation availability: Papers with linked code repos are more immediately actionable

### Paper Evaluation Framework

For any paper, answer:
1. **Claim:** What does the paper claim to have achieved?
2. **Evidence:** What experiments support the claim? Are there ablations?
3. **Reproducibility:** Is code/data publicly available? Have others reproduced it?
4. **Applicability:** Is this technique applicable to your use case?
5. **Limitation:** What does the paper NOT address? What are the known failure modes?

### Knowledge Management System

Build a second brain for AI research:
- **Notion or Obsidian:** Research notes linked by topic; tagging system for capability area
- **Zotero or Readwise:** PDF annotation and citation management
- **Weekly digest:** Force yourself to synthesize learnings weekly, not just save papers
- **Quarterly landscape review:** Update your model of the AI landscape; which assumptions have changed?

---

## 9. Fine-Tuning vs. RAG vs. Prompt Engineering Decision Framework

### Decision Tree

```
Can you solve the problem with better prompts alone?
  → Yes: Start with prompt engineering. It's faster, cheaper, reversible.

Does the problem require up-to-date or proprietary information not in the model?
  → Yes: Use RAG. Don't fine-tune models to memorize facts — models forget and hallucinate.

Does the problem require a specific output format, style, or behavior that 
prompts alone can't consistently achieve?
  → Yes: Consider fine-tuning (instruction fine-tuning).

Does the problem require domain-specific knowledge or terminology that the 
base model consistently gets wrong?
  → Yes: Consider fine-tuning on domain data.

Does the problem require sub-$0.001/call cost at very high volume?
  → Yes: Fine-tune a small model (Llama 3.1 8B, Mistral 7B, Phi-3).
```

### When Each Approach Wins

**Prompt Engineering wins when:**
- Task is well-defined and a few-shot examples work
- Baseline model already has the knowledge needed
- Iteration speed matters (prompts change in seconds; fine-tuning takes hours/days)
- Volume doesn't justify fine-tuning cost

**RAG wins when:**
- Information is too large to fit in context window
- Information changes frequently (news, product catalog, documentation)
- Precise factual recall required (reduces hallucination for grounded facts)
- Regulatory requirement for source citation and auditability

**Fine-Tuning wins when:**
- Consistent output format/style required at scale (JSON structure, tone, persona)
- Domain-specific terminology and abbreviations used extensively
- Latency/cost optimization through smaller model
- Privacy: cannot send data to third-party API (fine-tune on private cluster)

### RAG vs. Fine-Tuning: The Classic Tradeoff

| Dimension | RAG | Fine-Tuning |
|-----------|-----|-------------|
| Knowledge update | Real-time | Requires retraining |
| Fact precision | High (retrieval-grounded) | Medium (memorized, can drift) |
| Cost | Higher at inference (retrieval + generation) | Lower at inference (no retrieval) |
| Latency | Higher (retrieval adds 50-200ms) | Lower |
| Setup complexity | Higher (vector store, chunking, embedding) | Lower at inference |
| Training data needed | None | Yes (100-10,000 examples) |

**Combined approaches:** Many production systems use both. Fine-tune for style/format/behavior; RAG for factual grounding.

---

## 10. Open-Source vs. Proprietary Model Tradeoffs

### Comparison Matrix

| Dimension | Proprietary (GPT-5, Claude) | Open-Source (Llama, Mistral) |
|-----------|---------------------------|------------------------------|
| Quality ceiling | Higher (as of mid-2026) | Catching up fast |
| Cost at scale | $0.50-5.00/M input tokens | $0.10-0.50/M (self-hosted) or free |
| Data privacy | Data sent to third party | Full on-premise control |
| Customization | Limited (fine-tune API, prompting) | Unlimited (weights owned) |
| Compliance | SOC 2, GDPR available via enterprise | Full control, you certify |
| Vendor lock-in | High | None |
| Maintenance burden | None | Significant (infra, security, updates) |
| Latency at scale | Varies by provider | Optimizable with vLLM, TGI |

### Open-Source Model Landscape (2025-2026)

| Model | Parameters | Use Case |
|-------|-----------|---------|
| Llama 4 Scout | ~170B (mixture of experts) | Best open-weights for complex tasks |
| Llama 3.3 70B | 70B dense | Best quality/compute ratio in class |
| Mistral Large 2 | 123B | Strong multilingual, function calling |
| Mixtral 8x22B | 141B MoE | Cost-efficient quality |
| Phi-4 (Microsoft) | 14B | Best-in-class small model for reasoning |
| Qwen2.5-Coder 32B | 32B | Best open-source code model |
| DeepSeek V3.2 | ~671B MoE | Near-frontier quality, open weights |

### Self-Hosting Stack (2025)

**Inference engines:**
- **vLLM:** Highest throughput for production serving; PagedAttention for memory efficiency; OpenAI-compatible API
- **TGI (Text Generation Inference):** Hugging Face's serving solution; enterprise support available
- **Ollama:** Developer-friendly local inference; single binary; great for development
- **llama.cpp:** C++ inference; works on CPU and GPU; lowest memory footprint

**Recommended stack for production self-hosted:**
- **GPU:** NVIDIA A100 (40/80GB) or H100 for larger models; A10G for 7-13B models
- **Orchestration:** Kubernetes + Ray Serve or vLLM
- **API layer:** FastAPI + vLLM OpenAI-compatible server
- **Monitoring:** Prometheus + Grafana for GPU utilization, QPS, latency, error rate

---

## 11. AI Hardware Landscape

### GPU Comparison (2025-2026)

| GPU | VRAM | Performance | Use Case |
|-----|------|-------------|---------|
| NVIDIA H200 | 141GB | Highest | Large model training and inference |
| NVIDIA H100 | 80GB | Excellent | Production training and large inference |
| NVIDIA A100 | 40/80GB | Very Good | Standard production inference |
| NVIDIA A10G | 24GB | Good | 7-13B model inference |
| NVIDIA RTX 4090 | 24GB | Good | Developer workstations |
| AMD MI300X | 192GB | Excellent | High memory density; ROCm ecosystem |
| Google TPU v5p | — | Excellent | Google Cloud; JAX/PyTorch natively |
| AWS Trainium 2 | — | Strong | AWS-native; cost-competitive with H100 |

### Model-to-Hardware Sizing

| Model Size | Minimum VRAM (FP16) | Recommended |
|-----------|--------------------|----|
| 7B | 14GB | 2× A10G or 1× A100-40G |
| 13B | 26GB | 1× A100-40G or 2× A10G |
| 34B | 68GB | 2× A100-40G or 1× A100-80G |
| 70B | 140GB | 2× A100-80G or 1× H100-80G |
| 405B | 810GB | 10× A100-80G (tensor parallel) |

### Inference Optimization Techniques

**Quantization:**
- **INT8 (GPTQ, AWQ):** 2x memory reduction; < 1% quality degradation for most tasks
- **INT4 (GPTQ 4-bit, GGUF Q4):** 4x memory reduction; 2-5% quality degradation
- **FP8:** New standard supported by H100 and A100; excellent quality-memory tradeoff
- Recommendation: Use INT8 for production quality; INT4 for resource-constrained or low-stakes tasks

**KV Cache optimization:**
- PagedAttention (vLLM): Treats KV cache like virtual memory paging; prevents fragmentation; 3-4x throughput increase
- Continuous batching: Process incoming requests in parallel rather than waiting for batch to fill

**Speculative decoding:**
- Use small "draft model" to speculatively generate tokens; large model validates in parallel
- 2-3x speedup with minimal quality loss
- Best for interactive applications where latency matters more than throughput

---

## 12. Competitive AI Product Landscape Analysis

### Framework for AI Product Analysis

**Five dimensions:**
1. **Capability differentiation:** What does this product do better than alternatives?
2. **Distribution moat:** How does it reach users? (API, embedded, standalone?)
3. **Data flywheel:** Does usage generate proprietary training data?
4. **Pricing model:** Usage-based, per-seat, or freemium? How aggressive?
5. **Developer ecosystem:** SDKs, plugins, integrations, MCP servers?

### Current Competitive Landscape (June 2026)

**AI Foundation Model Providers:**
- Anthropic (Claude 4.x) — strongest for reasoning, safety, long context, code
- OpenAI (GPT-5 family, o3) — strongest for ecosystem, multimodal, real-time
- Google DeepMind (Gemini 2.5) — strongest for long context (2M), video, search integration
- xAI (Grok 4) — lowest cost frontier, largest context, real-time web search built-in
- Meta (Llama 4) — best open-weights; enables fine-tuning and self-hosting
- Mistral AI — best European/open option; multilingual strength

**AI Coding Assistants:**
- GitHub Copilot (50M+ users) — dominant market share
- Cursor (fast growth; Claude-powered; IDE-native)
- Windsurf (Codeium) — strong market position
- JetBrains AI — strong for JetBrains IDE users

**AI Agents/Automation:**
- Claude Code — top software engineering agent
- Devin / SWE-agent — autonomous software engineering
- AutoGPT, CrewAI, LangGraph — agent framework ecosystem

---

## 13. Building an Internal AI Capability Assessment Process

### The AI Capability Assessment Lifecycle

**Phase 1: Landscape scan (quarterly)**
- Survey of new models, tools, and deployment patterns
- Output: Updated model capability matrix; "watch list" of promising new capabilities

**Phase 2: Use case mapping (semi-annual)**
- Workshop with business units to identify AI-addressable opportunities
- Prioritize by: value potential, feasibility, data availability, regulatory risk
- Output: Prioritized use case pipeline

**Phase 3: Proof of concept (2-4 weeks per use case)**
- Build minimum viable AI integration
- Measure against pre-defined success metrics
- Output: Go/No-go decision for production investment

**Phase 4: Production evaluation (8-12 weeks)**
- A/B test with real users
- Monitor accuracy, latency, cost, user satisfaction
- Output: Ship decision with full metrics

**Phase 5: Continuous monitoring (ongoing)**
- Track model performance over time (data drift, model drift)
- Re-evaluate model selection every 6 months
- Monitor for capability improvements from providers

### AI Readiness Assessment for Organizations

Score your organization 1-5 on each:
- **Data:** Do you have labeled data for your use cases? Is it accessible and clean?
- **Infrastructure:** Can you deploy and scale ML models? Do you have GPU access?
- **Talent:** Do you have engineers who can integrate LLM APIs? ML engineers for fine-tuning?
- **Process:** Do you have evaluation workflows? CI/CD for ML?
- **Governance:** Do you have AI use policies? Privacy review for AI data use?

Readiness score ≥ 20: Ready for production AI. Score 15-19: Investment needed before scaling. Score < 15: Start with off-the-shelf tools; build internal capability in parallel.

---

## Quick Reference: AI Discovery Decision Matrix

| Question | Answer |
|----------|--------|
| Which benchmark to use for model selection? | GPQA Diamond + SWE-bench + your own task-specific eval |
| Frontier vs. mid-tier model? | Frontier for complex reasoning; mid-tier for high-volume, cost-sensitive |
| Fine-tune vs RAG? | RAG for factual recall + recency; fine-tune for style/format consistency |
| Open-source vs proprietary? | Proprietary for quality ceiling; open-source for privacy/cost/customization |
| How often to re-evaluate models? | Every 6 months; immediately when a major new model releases |
| GPU for 70B model serving? | 2× A100-80G minimum; H100 for production quality/throughput |
| Best coding benchmark 2026? | SWE-bench Verified (practical) + LiveCodeBench (contamination-resistant) |
| How to track AI research? | arXiv cs.AI/LG/CL + 10-15 key researchers + paperswithcode.com |
