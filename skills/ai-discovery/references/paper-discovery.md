# Paper Discovery & Triage Reference

## Primary Discovery Channels

### 1. arXiv — The Raw Feed

arXiv is the ground truth for AI/ML preprints. Key categories to monitor:

| Category | Focus | Signal Quality |
|----------|-------|---------------|
| **cs.CL** | NLP, language models, dialogue | Very high for LLM work |
| **cs.LG** | Machine learning theory and methods | High; broad |
| **cs.AI** | AI systems, planning, reasoning | High for AGI-adjacent work |
| **cs.CV** | Computer vision, multimodal | High for vision-language |
| **stat.ML** | Statistical learning, theory | High for foundational methods |

**How to work the arXiv feed:**
- Daily listings at `arxiv.org/list/cs.CL/recent` (and other categories) show submissions within 24 hours
- Filter aggressively by title scan — you're looking for: novel architectures, new benchmark records, surprising capability demos, scaling results, efficiency breakthroughs
- Cross-list papers (tagged to multiple categories) often signal interdisciplinary breakthroughs
- Abstract structure tell: strong papers front-load the contribution claim. If the first sentence doesn't state a clear, falsifiable contribution, read with lower prior.

**arXiv triage heuristics:**
- "We propose" + specific method name → evaluate methodology
- "State-of-the-art on X, Y, Z" → check if benchmarks are saturated or fresh
- "We release" → check if weights/code/data are actually public
- Affiliation matters: Google DeepMind, OpenAI, Anthropic, Meta FAIR, MIT, Stanford, CMU, UCL, ETH Zurich have high prior probability of impact

### 2. HuggingFace Daily Papers — Curated Signal

`huggingface.co/papers` is the highest-signal daily digest for applied ML.

**How it works:**
- Papers are submitted by community members (researchers, practitioners)
- Upvoted by the ML community in real-time
- AK (Abhishek Thakur at HF) curates the initial selection daily
- Trending papers surface via community engagement — comments, likes, associations with models/datasets

**Using it effectively:**
- Check daily, spend 5 minutes scanning titles and upvote counts
- High upvote + released weights/spaces = immediate practical value
- Papers with associated HF model cards indicate reproducibility
- The `huggingface.co/papers/trending` feed aggregates multi-day momentum
- `huggingface-paper-explorer.vercel.app` offers enhanced filtering

**Signals to track:**
- A paper with >500 upvotes in 24 hours is breaking through noise
- Papers where the authors immediately release a Space (demo) → practical team
- Papers that generate model releases on HF within 24-48 hours → community traction

### 3. Papers With Code — Implementation Signal

`paperswithcode.com` adds a crucial layer: does the paper have working code?

**What to look for:**
- State-of-the-art tables: track which method holds each benchmark, how often the top changes
- Code availability badge: a paper without code has lower reproduction confidence
- Trending repos section: shows what practitioners are actually implementing
- Task pages: define the evaluation landscape for a given problem (e.g., "question answering", "code generation")

**Discovery workflow:**
- Navigate by task (e.g., "Code Generation") → see benchmark history → spot the inflection points
- "New at top" = something displaced the prior SOTA; read why
- Repos with rapid star accumulation = practitioner validation

### 4. Semantic Scholar — Citation Intelligence

`semanticscholar.org` provides citation graph analysis.

**Key signals:**

**Citation velocity** = citations per month since publication, especially in the first 3 months. Normal for an ML paper: 0-5 citations/month. Breakout signal: 20+ citations/month in month 1 means the community immediately recognized value.

How to compute it:
1. Find the paper on Semantic Scholar
2. Note "Highly Influential Citations" count (these are papers where the work is central, not just a footnote)
3. Divide by months since publication
4. Compare to field average (visible on the platform)

**Highly Influential Citations** are the true signal. A paper with 200 total citations but 50 "highly influential" has penetrated thinking more deeply than one with 800 citations and 10 influential.

**The citation graph for provenance:**
- Follow "References" to understand what a paper builds on — this reveals whether the approach is incremental vs. genuinely new
- Follow "Cited By" to see whether the work is being extended or just acknowledged

---

## The 3-Layer Paper Triage Framework

A systematic method for evaluating papers fast. Total time for a pass-1 triage: 3-5 minutes.

### Layer 1: Abstract Scan (60 seconds)
Read only the abstract. Answer:
1. **What is the claimed contribution?** (must be specific and falsifiable)
2. **What benchmark or task does it improve?** (check if this benchmark is saturated or fresh)
3. **Does it release code/weights/data?** (check "Implementation" or repository links)
4. **Is the institution high-prior?** (top lab or emerging lab with track record)

Decision: **Skip** / **Queue for Layer 2** / **Read Now**

### Layer 2: Results + Figures (2-3 minutes)
Jump directly to the results section and figures. Answer:
1. **What are the actual numbers?** (not just "outperforms baselines" — what are the gap sizes?)
2. **What is the comparison set?** (are they comparing to recent strong baselines, or cherry-picking old ones?)
3. **Does the improvement hold across all evaluations or only some?** (narrow wins are weaker signals)
4. **Are there ablations?** (ablation studies = the team actually understands *why* it works)
5. **Are error bars reported?** (no uncertainty = lower credibility)

Decision: **Reject** / **Bookmark** / **Read Now**

### Layer 3: Methodology (5-15 minutes)
Now read the method section. Answer:
1. **Is the mechanism novel or incremental?** (Is this a new idea, or applying existing ideas to a new domain?)
2. **What are the limitations section claims?** (Strong papers have honest limitations sections)
3. **Is the computational cost reported?** (FLOPs, GPU-hours, memory requirements)
4. **Is the approach reproducible?** (Could you implement this from the description + code?)
5. **What is the most likely failure mode?** (What edge cases would break this approach?)

Decision: **Deep study** / **File and revisit** / **Pass to team**

---

## Citation Velocity as a Discovery Signal

Papers that will matter show early citation acceleration. The window to catch them is months 1-3.

**Tier 1 — Breakout (>30 citations/month, >10 highly influential)**
These are redefining an area. Study deeply. Examples of this tier historically: Attention Is All You Need, GPT-3, LoRA, Flash Attention, Chain-of-Thought Prompting.

**Tier 2 — High Signal (10-30 citations/month, 3-10 highly influential)**
Solid contributions actively being built on. Worth deep engagement.

**Tier 3 — Field Standard (5-10 citations/month)**
Advancing the field incrementally. Track and file.

**Tier 4 — Niche (0-5 citations/month)**
May be highly relevant to a specific problem but not reshaping the field.

**Caveat:** Very recent papers (< 1 month old) should not be penalized by citation velocity — the signal takes time to accumulate. For very new papers, use HF upvotes and practitioner discussion as early proxies.

---

## Advanced Discovery Tactics

### The "Graph Walk" for Emerging Areas
When a new research area emerges (e.g., diffusion LLMs in 2024-2025):
1. Find the 2-3 seed papers that started it
2. Use Semantic Scholar to enumerate everything citing those papers
3. Sort by citation velocity
4. Map the graph: which papers are extensions, which are critiques, which are applications?
5. Identify the "missing piece" — what problem remains unsolved that multiple teams are racing toward?

### The "Lab Tracker" Workflow
Each major lab has signature areas of focus:
- **Anthropic**: Constitutional AI, scalable oversight, interpretability, long-context
- **Google DeepMind**: Multimodal, scientific AI (AlphaFold, genomics), RL at scale, Gemini architecture
- **OpenAI**: RLHF/RLAIF, reasoning models (o-series), multimodal, code generation
- **Meta FAIR**: Open-weight models (Llama series), efficient fine-tuning, social AI
- **DeepSeek**: MoE efficiency, RL for reasoning, low-cost training at scale
- **Mistral**: Efficient dense/sparse models, European open-source AI
- **AI2 (Allen Institute)**: Open science, dataset quality, reasoning benchmarks (MMLU origin)
- **EleutherAI**: Open-source language models, evaluation frameworks, dataset curation

Track each lab's preprint page and blog. Lab releases often pre-announce benchmark claims before the paper drops.

### The "Practitioner Lead" Signal
Researchers and ML engineers on X/Twitter often discuss papers within hours. High-signal accounts to monitor (as of 2025):
- Yann LeCun, Andrej Karpathy, Ilya Sutskever, Yoshua Bengio (opinion leaders)
- Lilian Weng, Sebastian Ruder, Elvis Saravia (synthesis and explanation)
- François Chollet, George Hotz (iconoclastic takes worth stress-testing)
- AK (abidlabs), Niels Rogge, Omar Sanseviero (HuggingFace practitioners)

When 3+ high-signal researchers discuss the same paper within 48 hours, that's a 95th percentile signal.
