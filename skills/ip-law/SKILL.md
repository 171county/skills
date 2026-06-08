---
name: ip-law
description: Expert-level skill covering intellectual property law for technology companies and AI startups. Covers patent eligibility under Alice/Mayo, AI inventorship and authorship doctrine, copyright in AI-generated works, training data liability, trade secret protection, open-source licensing, and defensive IP strategy. Use this when analyzing IP risk, drafting IP strategy, evaluating licensing terms, or advising on AI-generated content ownership.
---

# IP Law Expert

> **Disclaimer:** This skill provides educational legal information, not legal advice. Consult qualified IP counsel for specific legal matters.

---

## Patent Law: Software and AI Patents

### The Alice/Mayo Framework (§101 Patent Eligibility)

The two-step framework governing whether software and AI inventions are patentable in the United States:

**Alice Corp. v. CLS Bank International (2014, SCOTUS):**
- Step 1: Is the claim directed to an **abstract idea**, **law of nature**, or **natural phenomenon**?
- Step 2 (Alice Step 2A/2B post-2019 guidance): Does the claim add an **inventive concept** that transforms the abstract idea into a patent-eligible application?

**Mayo Collaborative Services v. Prometheus Labs (2012, SCOTUS):**
- Confirms that laws of nature are not patent-eligible
- Mental processes, mathematical relationships, and algorithms = abstract ideas

### AI-Specific Patent Eligibility (2025)

**USPTO 2024 AI Subject Matter Eligibility Guidance:**
- A claim directed to an AI system is eligible if it recites *specific technical improvements* to computer functionality, not just applying AI to a business problem
- Merely claiming "using a neural network to classify X" = abstract → ineligible
- Claiming "a neural network with [specific architecture element] that reduces inference latency by [mechanism]" = potentially eligible

**Recentive Analytics v. Fox Corporation (Fed. Cir. 2025):**
- The Federal Circuit's first major AI patent eligibility ruling post-Alice
- Held: Claims to "machine learning" or "neural network training" as mere tools for performing otherwise-abstract functions do **not** provide an inventive concept
- Key quote: "Merely applying machine learning — without more — does not transform an abstract idea into patent-eligible subject matter"
- Implication: You must claim *how* the ML is implemented, not just *that* ML is used

### Drafting Patent Claims That Survive Alice

**What works:**
- Specific model architecture details (e.g., specific attention mechanism reducing hallucination rate)
- Hardware-software integration claims (model + accelerator co-design)
- Novel training data curation methods with specific technical steps
- Inference optimization techniques (novel attention patterns, quantization methods)

**What fails:**
- "Using AI to predict [outcome]"
- "Applying machine learning to analyze [data type]"
- "A computer-implemented method of [business process] using a neural network"

**Drafting pattern:** Lead with the technical problem solved, the specific technical mechanism, and the measurable technical result. Avoid business-outcome language in independent claims.

---

## AI Inventorship: The Law as of 2026

### Thaler v. Vidal (Fed. Cir. 2022, cert. denied SCOTUS 2023)

**Holding:** Only natural persons can be inventors under 35 U.S.C. § 101. An AI system (DABUS, created by Stephen Thaler) cannot be named as an inventor on a US patent application.

**Why it matters:**
- AI-generated inventions must be attributed to the human(s) who "conceived" the invention
- "Conception" = the formation in the mind of a definite and permanent idea of the complete and operative invention
- If an AI generates an invention without human conception, there may be no valid inventorship → the patent is void

**Practical implication for AI companies:**
- Document human creative contribution to all AI-assisted inventions
- "AI-assisted invention" = human provided the problem, constraints, selected from AI outputs → human conceiver
- "AI-generated invention" = AI produced without human conceptual direction → no eligible inventor → unpatentable in the US
- Keep lab notebooks, Slack histories, commit messages showing human decisions in the invention process

### Foreign AI Inventorship

- **UK**: Thaler v. Comptroller-General (2023, UK Supreme Court) — confirmed same position as US
- **EU**: EPO consistently refuses AI-named inventors
- **Australia**: Full Federal Court reversed Thaler (2022) — reversed again on appeal (2023), now aligned with US/UK
- **No jurisdiction currently accepts AI as inventor** as of June 2026

---

## AI Authorship: Copyright Doctrine

### Thaler v. Perlmutter (D.D.C. 2023, affirmed D.C. Cir. 2025, SCOTUS cert. denied 2026)

**Holding:** Copyright requires human authorship. AI-generated images (Thaler's "A Recent Entrance to Paradise," created by DABUS's image system) cannot be copyrighted because there was no human author.

**D.C. Circuit (2025) clarification:**
- Affirmed: purely AI-generated works are not copyrightable
- "The Copyright Act has never stretched so far" as to protect non-human expression

**US Copyright Office (USCO) AI Policy (May 2025 Report):**
- **Human-authored elements in AI-assisted works ARE copyrightable** — the human's selection, arrangement, and modification of AI outputs can be protected
- **Purely AI-generated elements are NOT copyrightable** — even if humans initiated the process
- Registration requirement: applicants must disclose AI-generated content in applications
- Threshold: if a human made "sufficient creative choices" in the final work, the human-authored portions are protected

**What this means for products:**
- AI-generated code, images, text, music produced by your product likely cannot be copyrighted by the user or by you
- But UI design, curation, system prompts, selection/arrangement of outputs = potentially protected
- Your *system* (the trained model, the product architecture) can be copyrighted as a whole work; individual outputs may not be

### Practical Copyright Strategy for AI Products

1. **Document human creative choices**: maintain evidence of human editing, selection, and arrangement in any AI-assisted workflow
2. **Terms of Service clarity**: disclaim ownership of raw AI outputs; assign curated outputs to users
3. **Open content strategy**: if your content can't be copyrighted, publish it openly to prevent competitors from copyrighting it

---

## Training Data: Copyright and Fair Use

### The Training Data Copyright Wars

**Bartz v. Anthropic (N.D. Cal., settled March 2025, $1.5B):**
- Class action by authors (including comedian Sarah Silverman's estate successors) alleging Claude training data included copyrighted books
- Settlement: $1.5B fund, opt-in for copyright holders, Anthropic developed enhanced provenance system
- **The doctrinal question was not resolved** — fair use for training was never decided on the merits

**Key pending cases (as of June 2026):**
- *The New York Times v. OpenAI* — largest pending case; NYT alleges GPT memorizes and reproduces articles verbatim
- *Getty Images v. Stability AI* — image model training; watermark reproduction evidence is damaging
- *Authors Guild v. Google* (analog) — Google Books won fair use in 2015; training cases may follow this precedent

**US Copyright Office May 2025 Preliminary Report findings:**
- Fair use analysis for AI training is "highly fact-specific"
- Commercial AI training is *less* likely to qualify as fair use than academic/research training
- Verbatim reproduction in outputs is strong evidence against fair use
- Transformative use (statistical relationships) is stronger argument than mere copying

### Training Data Risk Mitigation

**Tier 1 (lowest risk):** Proprietary data you own; licensed data with training rights explicitly granted; public domain; Creative Commons CC0/CC-BY
**Tier 2 (moderate risk):** Web-scraped data; Reddit/social media with unclear terms
**Tier 3 (high risk):** Paywalled content; licensed databases without training rights; books/music/film

**Technical mitigations:**
- **Unlearning**: Techniques to remove specific training examples post-training (request from rights holder)
- **Differential privacy**: Adds noise during training to reduce memorization
- **Membership inference testing**: Test whether your model can recall specific training examples; quantify exposure

---

## Trade Secret Protection for AI Assets

### What AI Assets Qualify as Trade Secrets

Under the **Defend Trade Secrets Act (DTSA, 18 U.S.C. § 1836):**
A trade secret requires:
1. **Information** that derives economic value from not being generally known
2. **Reasonable measures** to maintain its secrecy

**AI assets that typically qualify:**
- **Model weights** (full-precision, trained model parameters) — primary asset for AI companies
- **Training data** (curated, cleaned, proprietary datasets)
- **System prompts** (especially for production agents)
- **Evaluation benchmarks** (internal, proprietary test sets)
- **Fine-tuning methodology** (specific PEFT configurations, training recipes)
- **Hyperparameter configurations** that produce superior performance

**Model weights as trade secrets — the key case:**
- *Waymo v. Uber* (settled 2018, $245M): established that ML model weights and training data are protectable trade secrets in transportation AI
- Post-Waymo: standard practice is to classify model weights as the company's crown jewel trade secret

### DTSA Practical Requirements

**To maintain trade secret status:**
1. **Confidentiality agreements**: PIIA for all employees; NDA for contractors and investors
2. **Access controls**: Model weights stored in HSM (Hardware Security Module) or encrypted blob storage; access logs required
3. **Employee offboarding protocol**: Remote wipe, return of equipment, exit interview with IP attestation
4. **Whistleblower notice requirement (DTSA §1833)**: All NDAs and PIIAs must include the DTSA whistleblower immunity notice — failure invalidates exemplary damages and attorney's fees claims

**DTSA whistleblower notice (mandatory language):**
```
NOTICE: Pursuant to 18 U.S.C. § 1833(b), an individual shall not be held 
criminally or civilly liable under any Federal or State trade secret law for 
the disclosure of a trade secret that is made in confidence to a Federal, 
State, or local government official or to an attorney solely for the purpose 
of reporting or investigating a suspected violation of law.
```

### Trade Secret vs. Patent Decision Framework

| Factor | Trade Secret | Patent |
|---|---|---|
| Duration | Perpetual (while secret maintained) | 20 years from filing |
| Cost | Low (operational security costs) | High ($50K–$200K per patent) |
| Protection | Against misappropriation only | Against independent discovery |
| Disclosure | Zero disclosure required | Full public disclosure |
| Best for | Model weights, data, proprietary methods | Specific technical implementations, hardware |

**AI startup rule of thumb:** Patent the application-layer innovations (specific use cases, novel UI interactions); trade-secret protect the model weights and training data.

---

## Open-Source Licensing Strategy

### The License Landscape for AI

**Permissive (business-friendly):**
- MIT, Apache 2.0: Can use in proprietary products; preferred for infrastructure libraries
- Apache 2.0 adds patent grant — strongly prefer over MIT when patent risk exists

**Copyleft (viral):**
- **GPL v3**: Modified distributed code must be released under GPL. The "SaaS loophole": running GPL code as a service (not distributing it) does NOT trigger the copyleft requirement.
- **AGPL v3 (GNU Affero GPL)**: Closes the SaaS loophole. Running AGPL-licensed code as a network service triggers copyleft on modifications. **Do not use AGPL-licensed dependencies in a commercial SaaS product without counsel review.**

**AI-specific licenses:**
- **Llama 4 Community License**: Permissive for use/modification but requires: (a) attribution, (b) Meta approval for deployments > 700M users, (c) no sublicensing for competing foundation model development
- **Stable Diffusion**: Modified ML Model License — restricts use in certain harmful categories
- **RAIL licenses (Responsible AI License)**: Use-case restrictions (prohibit harmful applications); no OSI-approved

### Open-Core Business Model

The dominant open-source monetization strategy for AI infrastructure:

```
Open-Source (Community Edition)   Enterprise (Paid)
──────────────────────────────    ────────────────────────────────
Core model/library                 SSO, RBAC, audit logging
Basic API                          SLA, enterprise support
Self-hosted only                   Managed cloud + SOC 2 Type II
                                   Advanced features (fine-tuning UI)
```

**Examples:** LangChain (open core), Weaviate (open core), Qdrant (open core), MLflow (Apache, monetized via Databricks)

### Dual Licensing

Offer the software under both GPL (free for open-source projects) and a commercial license (paid for proprietary use). Requires copyright assignment from all contributors (CLA — Contributor License Agreement).

**Companies using this:** MongoDB, Elasticsearch (pre-BSL switch), Qt, MySQL

### Business Source License (BSL / BUSL)

HashiCorp's (now IBM) controversial move: BSL 1.1 is **not** OSI-approved open source:
- Free to use with restrictions (no commercial SaaS competition) for 4 years
- Converts to Apache 2.0 after 4 years
- OpenTofu (Terraform fork) was community response

**AI relevance:** Some AI infrastructure companies (e.g., Redis transition attempt) are exploring BSL-style licenses to prevent hyperscalers from hosting their products as managed services.

---

## Defensive IP Strategy

### Open Invention Network (OIN)

The OIN is a royalty-free cross-licensing community covering the Linux ecosystem (and now expanded to include open-source AI infrastructure). **Joining OIN is free and provides defensive patent coverage** — OIN members cannot assert patents against Linux-related software.

**AI companies should consider OIN membership if:**
- Their product is built on open-source AI infrastructure
- They fear patent trolls targeting foundational AI concepts
- They want to signal openness to the developer community

### Patent Pools and Pledges

- **Open COVID Pledge model**: Commit to non-assertion of patents in specific domains
- **Google's LOT Network**: Protects against patent assertion entities (PAEs/trolls) using patents acquired from members
- **AI patent pledge (OpenAI, Salesforce)**: Some companies pledge not to assert AI patents offensively; useful for talent/reputation

### Freedom to Operate (FTO) Analysis

Before shipping a new AI feature, conduct FTO:
1. Search USPTO, EPO full-text databases for relevant claims
2. Map your technical implementation to claim elements
3. Identify potentially infringed patents
4. Options: design around, license, invalidate (IPR petition), or accept risk

**High-risk AI patent areas (frequent assertions):**
- Natural language processing for specific domains (legal, medical)
- Recommendation systems
- Fraud detection using ML
- Specific neural network training optimization methods

---

## Equity and IP: Founder Considerations

### Intellectual Property Assignment Agreements (PIIA)

**Critical:** All employees and contractors must sign a PIIA before doing any work. Assigns to the company all IP created in connection with their work.

**California PIIA carve-out (Cal. Labor Code § 2870):**
- Employees *cannot* be required to assign IP developed entirely on their own time, without company resources, and unrelated to company business or reasonably anticipated work
- Best practice: have employees explicitly list any pre-existing IP (prior inventions schedule) before signing

### The 83(b) Election and IP Vesting

When founders receive stock subject to a vesting schedule (treated as restricted property):
- Without 83(b): taxed as ordinary income at the fair market value on each vesting date
- With 83(b): taxed on the grant date fair market value only (typically near-zero for founders)
- **Deadline: 30 days from grant — absolute, no extensions, no exceptions**
- **File with the IRS (Form 15620 since 2023) AND your company** within 30 days
- Failure to file 83(b) can result in millions of dollars of ordinary income tax at IPO/acquisition

### Work Made for Hire

Code written by employees during employment = automatically owned by employer (work made for hire under 17 U.S.C. § 101).

**Exceptions that require separate assignment:**
- Work by independent contractors (must be in a written assignment agreement — work made for hire doctrine doesn't apply to most contractor work)
- Pre-employment work (must be explicitly assigned; PIIA handles this via prior inventions schedule)

---

## IP Due Diligence Checklist (For M&A and Fundraising)

```
Patent Portfolio
☐ All patents/applications assigned to the company (not founders/employees)
☐ No third-party claims on filed patents
☐ FTO analysis for core product features
☐ US/international prosecution status for pending applications

Copyright
☐ All code ownership clear (no unsigned contractor work)
☐ Open-source license compliance (no AGPL in SaaS stack without review)
☐ Training data provenance documented and licensed
☐ No unresolved DMCA takedown notices

Trade Secrets
☐ PIIA signed by all employees and contractors (including DTSA notice)
☐ Model weights stored securely with access logging
☐ NDAs in place with all third-party vendors
☐ Offboarding protocol documented and followed

Domain and Brand
☐ Trademarks registered (USPTO) for core brand and product names
☐ Domain names registered (including .ai, .io variants)
☐ No conflicting marks in relevant classes (Nice Classification)
```
