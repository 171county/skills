# AI Tooling Discovery & Competitive Intelligence

## Daily Discovery Workflow

A systematic 20-30 minute daily routine that surfaces 90% of meaningful developments:

### Morning Pulse (10 min)
1. **HuggingFace Daily Papers** (`huggingface.co/papers`) — scan titles, check upvotes. Read abstracts of anything with >100 upvotes or that touches your focus areas.
2. **GitHub Trending** (`github.com/trending?l=python&since=daily`) — filter by Python or language of choice. New repos with 500+ stars in 24h = practitioner signal.
3. **Twitter/X** — search `(filter:links lang:en) (arxiv OR HuggingFace OR "we release")` from accounts in your follow list. Takes 3 minutes; high signal-to-noise.

### Weekly Deep Dive (20-30 min)
1. **HuggingFace Trending Papers** (`huggingface.co/papers/trending`) — multi-day accumulated signal.
2. **Papers With Code** — check state-of-the-art tables for your focus benchmarks. New #1 = read the paper.
3. **AI Newsletter Digest** — batch newsletters for weekly reading rather than interrupting daily.
4. **Lab Blogs** — OpenAI blog, Anthropic research, Google DeepMind, Meta AI blog, Mistral blog. Labs often publish blog posts 24-72 hours before the paper drops.

---

## AI Discovery Platforms

### HuggingFace Ecosystem
**Daily Papers**: Community-curated, upvote-ranked. Since launch has featured 10K+ papers. AK (Abhishek Thakur) seeds the initial selection; community surfaces more.

**Trending Models**: `huggingface.co/models?sort=trending` — models gaining download/like velocity. Surge in a model = community is finding it useful.

**Trending Spaces**: `huggingface.co/spaces?sort=trending` — live demos. A trending Space means practitioners are actively experimenting with a capability.

**Paper Explorer** (`huggingface-paper-explorer.vercel.app`): Enhanced filtering by task, date, upvotes.

**Datasets Trending**: New datasets often precede capability jumps — a new high-quality dataset for a task is a forward signal.

### GitHub Discovery
**Trending** (`github.com/trending/python?since=weekly`): Filter by language and time window. Weekly is more reliable than daily for signal (less noise from social media bursts).

**Topic Search**: `github.com/topics/large-language-models`, `github.com/topics/rag`, `github.com/topics/llm-inference` — follow topics for continuous discovery.

**Star History** (`star-history.com`): Compare star growth curves across repos. Exponential inflection = community adoption happening now.

**What to look for in trending AI repos:**
- README quality: Does it explain what problem is solved and how?
- Stars/forks ratio: High forks = people building with it; high stars = people are interested but not using
- Recency of commits: Active maintenance signals
- Issue count and response time: Engaged maintainers
- Benchmarks in README: If they benchmark against known models, you can triangulate quality

### ProductHunt
`producthunt.com/topics/artificial-intelligence` — consumer and developer AI tools. Useful for:
- Spotting when a research capability becomes a product
- Identifying the practical use cases researchers are building toward
- Finding tools that wrap open-source models with UX innovation

Signal: A tool with >500 upvotes on launch day that builds on an open-weight model = the model has crossed the production viability threshold.

---

## AI Newsletter Stack

### Research-Focused (for staying at the frontier)
| Newsletter | Frequency | Focus | Signal Level |
|-----------|-----------|-------|-------------|
| **Import AI** (Jack Clark) | Weekly | Research breakthroughs, policy, lab tracking | Very High |
| **The Batch** (Andrew Ng / deeplearning.ai) | Weekly | Research + applications, authoritative perspective | High |
| **Nathan.ai** | Weekly | Practitioner synthesis, emerging tools | High |
| **Ahead of AI** (Sebastian Raschka) | Weekly | Deep technical dives on papers | High |
| **LLMs Research Newsletter** | Bi-weekly | Paper summaries, 10-min format | High |

### Practitioner-Focused (for tools and implementation)
| Newsletter | Frequency | Focus |
|-----------|-----------|-------|
| **The Rundown AI** | Daily | Quick news digest, tools, launches |
| **The Neuron** | Daily | Trends, tools, applications |
| **TLDR AI** | Daily | Curated tech news including AI |
| **Ben's Bites** | Daily | Links + commentary, product focus |

### Synthesis Approach
Don't read every newsletter in full. Instead:
1. Skim subject lines daily for anything marked "major release" or naming a model you track
2. Full-read the weekly research-focused newsletters on weekends
3. Use newsletters as an index, not as the primary source — follow links to original papers/repos

---

## Competitive Intelligence: Reading Between the Lines

### Model Release Signals

**Pre-announcement patterns:**
- Lab hires researchers with unusual specialization (e.g., surge in video ML hires → video model incoming)
- Job postings for "X infrastructure" (e.g., "real-time voice infrastructure" → voice model development)
- API endpoints appear in docs before announcement (caught multiple times by API explorers)
- System card or safety evaluation papers filed before release paper
- Blog posts about evaluation methodology → implies they're measuring something new

**Reading a technical report:**
What labs typically disclose vs. obscure:

| Usually Disclosed | Often Obscured |
|------------------|----------------|
| Benchmark scores | Training data composition |
| Parameter count | Post-training recipe details |
| Context window | RLHF reward model specifics |
| Architecture class (dense/MoE) | Actual compute budget |
| Release date | Fine-tuning data quality filters |

**What a new benchmark result really means:**
- Score on HLE/ARC-AGI/FrontierMath improvement → probably real reasoning gain (these are hard to game)
- Score on MMLU improvement → almost certainly noise or benchmark overfitting; ignore
- Score on SWE-bench improvement → real coding capability gain; scrutinize the task subset
- "Human-level on X" → ask: which humans? What's the comparison set? Human expert performance or crowdworker?

### Capability Jump Detection Framework

A genuine capability jump (not hype) shows 3+ of these signals:

1. **Novel architectural paper** released alongside the model — explains *why* performance improved
2. **Performance generalizes** across 3+ distinct benchmark families (not just one category)
3. **Third-party reproduction** within 2 weeks (LMSYS Arena ranking, external eval runs)
4. **Practitioners adopt** it within 30 days (GitHub repos using the model/API start trending)
5. **Cost curve shifts** — not just better, but achieving prior-best performance at lower cost
6. **Anomalous task performance** — something that felt intractable before now works reliably

### Tracking Open-Weight Model Releases

The open-weight ecosystem moves faster than closed. Key indicators that an open model matters:

- **Within 24h**: Hugging Face model card published, GGUF quantization available (for consumer hardware)
- **Within 48h**: Trending on HF models, Space demos running, first independent benchmark results
- **Within 1 week**: Fine-tuned variants appearing, merged models, integration into LM Studio/Ollama/vLLM
- **Within 1 month**: Community fine-tunes for specific domains, comparison against closed models normalizes

**Speed of quantization as a signal**: When a model has GGUF quantizations (for llama.cpp) available within hours of release, it means a sophisticated ML practitioner found it worth running immediately — strong prior that the model is high quality.

---

## AI Research Institutions: Who to Watch

### Tier 1 — Setting the Frontier

**Anthropic**
Focus: Constitutional AI, scalable oversight, long-context, interpretability, computer use. Most safety-rigorous outputs. Technical reports are detailed. Watch: interpretability research, Alignment Science team papers.

**Google DeepMind**
Focus: Multimodal (Gemini), scientific AI (AlphaFold, robotics, genomics), RL at scale, inference efficiency. Largest research org. Watch: Nature/Science publications for scientific AI, I/O announcements for capability jumps.

**OpenAI**
Focus: Reasoning (o-series), RLHF at scale, multimodal, code generation, AGI trajectory. Most commercially aggressive. Watch: system cards for surprising benchmark claims; blog posts for new capability demos.

**Meta FAIR**
Focus: Open-weight models (Llama), efficient fine-tuning (LoRA, QLoRA originated here), multimodal, social AI. Highest impact on open-source ecosystem. Watch: PyTorch integrations, new Llama releases.

**DeepSeek**
Focus: Training efficiency, MoE architecture, RL for reasoning, cost reduction. Demonstrated that frontier performance doesn't require frontier budgets. Watch: their arXiv preprints — small team, fast publication cycle.

### Tier 2 — High Impact

**Mistral AI**
Focus: Efficient dense/sparse models, European open-source AI, enterprise deployment. Mixtral established MoE viability for open-weight. Watch: new architecture papers and commercial releases.

**AI2 (Allen Institute for AI)**
Focus: Open science, dataset quality, evaluation robustness. Originated MMLU benchmark. OLMo project advances fully open (weights + data + code) model development. Watch: their evaluation papers and dataset releases.

**EleutherAI**
Focus: Fully open LLM research, the Pile dataset, evaluation frameworks. GPT-Neo/J/NeoX lineage. Smaller but high-integrity research. The Evaluation Harness is the gold standard open eval framework.

**Together AI / Cohere / Aleph Alpha**
Focus: Enterprise inference, retrieval-augmented, multilingual (Aleph Alpha for European), embedding models (Cohere). Watch for practical deployment innovations.

---

## TRIAGE Scoring Framework for AI Tools

When evaluating a new AI tool/library/model, score it on these 6 dimensions (1-5 each):

| Dimension | What to Assess |
|-----------|---------------|
| **T**ask Fit | How well does it solve a real, frequent problem? |
| **R**eproducibility | Can you run it yourself? Is there a demo/Colab/Space? |
| **I**nnovation | Is the core idea genuinely new or a wrapper? |
| **A**doption Signal | Stars, upvotes, active issues, community size |
| **G**ap to Production | How far from production-ready? (research → demo → API → stable) |
| **E**cosystem Fit | Does it integrate with standard stacks (HF, LangChain, PyTorch)? |

**Score interpretation:**
- 25-30: Immediate evaluation — this is potentially important
- 20-24: Queue for testing within 1 week
- 15-19: Monitor — may matter as it matures
- <15: Low priority unless it's highly relevant to a specific project

---

## Practical Evaluation Workflow for New Models

### Stage 1: Rapid Benchmark Triage (30 minutes)
1. Check LMSYS Arena ranking (if listed) — gives overall human-preference position
2. Pull benchmark numbers from the model card or technical report
3. Apply the saturation/contamination filter (see `references/benchmarks.md`)
4. Note: Is this open-weight or API-only? What's the pricing/license?
5. Decision: worth Stage 2 or not?

### Stage 2: Task-Specific Evaluation (2-4 hours)
Build a minimal eval set for your use case:
- 20-50 examples representative of your task
- Include 5-10 hard cases (edge cases, ambiguous inputs, adversarial examples)
- Baseline: your current best model on the same set
- Measure: accuracy, latency (time to first token + throughput), cost per task

**Rule:** Never deploy based on benchmark numbers alone. Your task distribution differs from benchmark distributions.

### Stage 3: Integration POC (1-2 days)
- Build a minimal integration with your production stack
- Test against real (or realistic) data at the margin size you expect
- Measure: latency under load, error rates, cost at projected volume
- Check: rate limits, terms of service, data handling

### Stage 4: Cost-Performance Analysis
Use a 3x3 grid:
- X-axis: Accuracy (low/medium/high on your task eval)
- Y-axis: Cost (low/medium/high — compare $/1K queries)
- Ideal quadrant: high accuracy, low cost
- Watch: models in the "medium accuracy, very low cost" quadrant may beat "high accuracy, medium cost" at scale

**Rule of thumb:** If a model achieves >95% of your target model's accuracy at <50% of the cost, it's worth serious consideration for volume use cases.
