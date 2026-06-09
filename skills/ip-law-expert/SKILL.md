---
name: ip-law-expert
description: Expert-level intellectual property law knowledge for tech startups and AI companies. Covers patent strategy (prosecution, PCT, continuation, FTO), copyright (software, AI-generated content, work-for-hire, DMCA), trademark (registration, enforcement, trade dress), trade secrets (DTSA, UTSA, reasonable measures), AI-specific IP (training data copyright, AI inventorship, model weights protection), open source licensing strategy (MIT/Apache/GPL/AGPL/SSPL), employee/contractor IP assignments (PIIA), and international IP. Use for any IP strategy, due diligence, or legal analysis question.
---

# IP Law Expert

> **Disclaimer:** Educational reference only; not legal advice. IP law is fact-specific and jurisdiction-dependent. Consult qualified IP counsel for specific matters.

---

## Patent Law

### Statutory Requirements (35 U.S.C.)

Four requirements must ALL be met:

| Statute | Requirement |
|---------|-------------|
| § 101 | Patentable subject matter (utility, process, machine, manufacture, composition) |
| § 102 | Novelty (not in prior art) |
| § 103 | Non-obviousness (not obvious to PHOSITA—person having ordinary skill in the art) |
| § 112 | Enablement + written description + definiteness |

Patents grant the right to **exclude** others for **20 years** from filing date. Trade-off: public disclosure.

### Types of Patent Applications

**Provisional Application (§ 111(b)):**
- Establishes priority date without formal claims
- 12-month pendency—must convert to non-provisional exactly within 12 months (no extensions)
- USPTO fees: $65 (micro), $130 (small), $320 (large entity)
- Professional cost: $4K–$10K for quality provisional
- **Critical warning:** A thin provisional filed before Demo Day may not support broad claims. If the USPTO finds your provisional inadequately supports non-provisional claims, your priority date reverts to non-provisional filing—potentially destroying novelty.

**Non-Provisional (Utility) Application:**
- Formal claims, specification, drawings, abstract
- Average time to first Office Action: ~19.9 months (FY2024)
- Total prosecution: 18–36 months typical; up to 4–5 years
- Attorney fees: $15K–$30K for software/tech applications

**Design Patent (§ 171):**
- Protects ornamental appearance of a functional item
- 15-year term from grant
- Prosecution: 12–18 months; ~$3K–$5K total
- Apple v. Samsung: Design patents on UI elements and device form factors yielded $1B+ in damages
- Startups with distinctive UI/UX should build design patent portfolios alongside utility patents

### Patent Prosecution

1. **Filing** → assigned to art unit/examiner
2. **Publication** → 18 months after earliest priority (can be accelerated to month 6)
3. **First Office Action (FOA)** → initial rejection or allowance
4. **Response** → amend claims and/or argue for allowance (3-month deadline from FOA)
5. **Final Rejection or Notice of Allowance**
6. **Appeals** → PTAB, then Federal Circuit

**RCE (Request for Continued Examination):** Continue prosecution after final rejection; restart examination on amended claims.

### Continuation Strategy

**Continuation:** Same disclosure, new claims; filed while parent is pending.
- Enables targeting competitor products with tailored claims
- "Pending continuation" = flexibility reservoir; respond to market developments
- No additional disclosure cost—leverage sunk investment

**Divisional:** Required when USPTO issues restriction requirement (multiple inventions in one application). Each divisional gets independent examination.

**Continuation-in-Part (CIP):** Adds new disclosure. New claims supported only by new matter get new (later) priority date. Use sparingly.

**Portfolio strategy:** Build interlocking claims at multiple scope levels—broad independent claims, narrower dependent claims as fallbacks, continuation applications targeting competitor design-arounds.

### Freedom to Operate (FTO) Analysis

**FTO ≠ patentability search.** FTO asks: "Can we sell this product without infringing someone else's unexpired patents?"

- Scope: Third-party *claims*, not your invention's novelty
- Cost: $5K–$20K depending on complexity and jurisdictions
- Average patent litigation cost: $2.3M–$4M per suit—FTO is cheap insurance
- When required: Before product launch, entering new markets, before capital raises (investors require it at Series A+)

---

## Software Patents Post-Alice

### The Alice Framework

***Alice Corp. v. CLS Bank International***, 573 U.S. 208 (2014) — two-step §101 eligibility test:

**Step 1:** Is the claim directed to a patent-ineligible concept?
- Abstract ideas (mathematical concepts, fundamental economic practices, mental processes)
- Laws of nature or natural phenomena

**Step 2 (if Step 1 = yes):** Does the claim contain an "inventive concept"—something **significantly more** than the abstract idea alone?
- Specific, concrete technical improvement beyond just "apply it on a generic computer"

### What Survives Alice

**Strong eligibility signals:**
- Improvements to **computer functioning itself**: novel data structures improving memory, faster algorithms, reduced GPU utilization
- Overriding "routine and conventional sequence of events" in computer technology (*DDR Holdings v. Hotels.com*)
- Self-referential database structure improving memory efficiency (*Enfish v. Microsoft*, 2016)
- Behavior-based virus scanning with structural code analysis (*Finjan v. Blue Coat*, 2018)

**Ineligible patterns:**
- Generic computer + abstract idea (applying existing ML to a new domain)
- **Key 2025 case:** *Recentive Analytics v. Fox Corp.*, 134 F.4th 1205 (Fed. Cir. 2025): Applying established ML methods to new data environment (TV scheduling) WITHOUT improving the ML process itself = ineligible. Cert denied Dec. 2025.
- Computerized intermediated settlement (Alice itself)
- AI "speed up" of what a human could do

### USPTO 2024 AI Guidance

Effective July 17, 2024—AI/ML patents need specific technical improvement:
- **USPTO Examples 47 and 48 (2025):** Frame AI claims around improvements to the AI system itself—training efficiency, model architecture improvements, novel preprocessing—NOT application to a new domain
- AI that achieves results **previously impossible** for humans may be eligible if technically specific

### Claims Drafting for Software/AI

To survive Alice:
1. Identify a **specific technical problem** in prior art
2. Describe a **specific technical solution** (novel attention mechanism, data augmentation pipeline, model architecture feature)
3. Claim specific data structures, operations, and computer components
4. Avoid purely functional claiming ("a means for doing X")
5. Frame as improvements to the computer system itself
6. For AI/ML: Claim novel training architectures, specific preprocessing, model evaluation/selection criteria—not just "train a model on domain X"

**Multiple claim types for coverage:**
- Method claims: steps performed by computer
- System claims: specific hardware/software arrangement
- CRM claims: instructions stored on non-transitory computer-readable medium

---

## Copyright Law

### What Copyright Protects

Under **17 U.S.C. § 102**, protects original works of authorship fixed in a tangible medium:
- Software code (source + object code—treated as "literary works")
- Databases (if selection and arrangement is original—*Feist v. Rural Telephone*, 499 U.S. 340 (1991): mere factual compilation not protectable)
- Website content, documentation, marketing materials
- User interface design elements (not functionality)
- AI-assisted content (with human authorship caveats—see AI section)

**Does NOT protect:** Ideas, facts, functional elements, systems, methods, processes, typefaces, titles, short phrases. The **idea-expression dichotomy** (§ 102(b)) is foundational.

### Registration Benefits

Copyright arises automatically on creation. But registration is critical:

1. **Standing to sue** (§ 411): Cannot file infringement suit until Copyright Office acts on registration application (*Fourth Estate v. Wall-Street.com*, SCOTUS 2019)
2. **Statutory damages + attorney fees** (§ 412): Only if registered **before infringement** or **within 3 months of first publication**
3. Statutory damages: $750–$30,000/work; up to $150,000 for willful infringement
4. Prima facie evidence of validity if registered within 5 years of publication
5. Customs recordation enables CBP to seize infringing imports

**Register promptly:** Most companies discover the need for registration only after infringement has occurred—by then statutory damages are unavailable.

**For software:** Use Form TX; can register with first/last 25 pages deposited (middle redacted as trade secret) per 37 C.F.R. § 202.20(c)(vii)(A).

### Work-for-Hire Doctrine

Under **17 U.S.C. § 101**, a "work made for hire" is:

1. Work prepared by an **employee within scope of employment** → employer owns copyright automatically
2. **Specially commissioned work** in one of nine enumerated categories + **written agreement**

**Critical:** Software is NOT on the nine categories list. An independent contractor's code is owned by the **contractor**, not the company, without an explicit written copyright assignment.

**Fix:** Every contractor agreement must have:
1. Work-made-for-hire clause
2. Present-tense copyright assignment ("**Contractor hereby assigns**…") as backup
3. Moral rights waiver for international applicability

### DMCA Safe Harbor (§ 512)

Platform immunity for user-uploaded content requires:
- No actual knowledge of infringement
- No direct financial benefit from infringement when able to control it
- DMCA agent registered with Copyright Office + takedown policy
- Repeat infringer termination policy

**Takedown process:** Rightholder sends § 512(c)(3) notice → Platform removes "expeditiously" → User may file counter-notification → Content restored after 10–14 days unless rightholder sues.

**Warning:** Before sending takedown notices, consider fair use (*Lenz v. Universal Music*, 9th Cir. 2015—failure to consider fair use creates liability risk).

### Fair Use (§ 107)

Four-factor balancing test:
1. Purpose and character (commercial vs. non-commercial; **transformative** use weighs heavily for fair use)
2. Nature of copyrighted work (factual/published → favors fair use)
3. Amount taken (quantity + whether the "heart" of the work was taken)
4. Effect on the market (most heavily weighted; harm to existing or derivative markets weighs against)

---

## AI-Specific IP

### AI Inventorship

***Thaler v. Vidal***, 43 F.4th 1207 (Fed. Cir. 2022), affirmed (cert denied 2023): **AI systems cannot be named as inventors**. "Inventor" under 35 U.S.C. § 100(f) requires a natural person.

CNIPA (China) implemented the same rule effective January 1, 2026.

**Practical implication:** Human engineers using AI tools (Copilot, Claude, ChatGPT) can be named as inventors if they make a qualifying inventive contribution—directing the AI, selecting among outputs, exercising creative judgment. Document human creative contributions to AI-assisted inventions.

### AI-Generated Copyright

Copyright Office positions (2024–2025 three-part report):

**Core principle:** Copyright requires human authorship. Pure AI-generated works without human creative input = **not copyrightable**.

***Thaler v. Perlmutter* (D.C. Cir. 2025, cert denied March 2026):** Cemented human authorship requirement.

**Spectrum of protection:**
- AI as spell-check/assistant (accepted/rejected suggestions) → human is still the author → copyrightable
- Human selects from AI outputs, arranges them → selection and arrangement may be copyrightable
- Minimal human input (simple prompt "write a poem about dogs") → NOT copyrightable
- Detailed iterative human prompting producing specific expression → possibly copyrightable; fact-specific

**Registration:** Must **disclose AI-generated content** in registration applications. Failure to disclose can invalidate registration.

### Training Data Copyright

**The central legal question:** Does using copyrighted works to train AI models constitute infringement, or is it fair use?

***Thomson Reuters v. Ross Intelligence* (D. Del., Feb. 2025)** — first major US ruling:
- Held: Ross's use of Westlaw headnotes to train competing AI was **NOT fair use**
- Fair use analysis: Commercial purpose, minimal transformation, wholesale copying, realistic market substitution harm
- Status: On appeal to Third Circuit; decision expected 2026–2027

**Pending major cases:**
| Case | Status |
|------|--------|
| Getty Images v. Stability AI (US) | Discovery; 12M+ images alleged |
| NYT v. OpenAI & Microsoft | Discovery |
| Andersen v. Stability AI | Trial Sept. 2026 |
| UMG/Concord v. Anthropic | Music lyrics; discovery |

**Interim risk management strategy:**
1. Use licensed training data (C4, Common Crawl with ToS review, licensed datasets)
2. Maintain data provenance records
3. EU AI Act requires training data transparency—implement early
4. Honor opt-out requests proactively

### Model Weights Protection

Model weights are the most valuable IP asset for a foundation model company.

**Trade secret protection (primary):**
- Keep weights confidential; API-only access; no weight distribution
- Robust access controls, audit logs, contractual obligations on API users
- Even if architecture is published, trained weights remain a trade secret
- Training cost alone (tens of millions) easily satisfies "commercial value" requirement

**Copyright protection (limited):**
- Numerical weight values arguably lack creative authorship
- Training code and model architecture documentation have clearer copyright protection
- No definitive case law on weight copyright

**Patent protection:**
- Novel training techniques, architectures, fine-tuning methods are patentable if meeting Alice
- Post-*Recentive Analytics*: Claims must show improvement to the ML process, not just application to a new domain

### API Terms of Service as IP Tool

Essential provisions:
- **No reverse engineering or extraction of model parameters**
- **No use of outputs to train competing models** (now standard for major LLM providers)
- **Copyright indemnification** for authorized API use (Anthropic, OpenAI, Google all offer this)—a competitive differentiator
- Clear ownership statement: customers retain rights to outputs generated through authorized use

---

## Trademark Law

### The Distinctiveness Spectrum

| Category | Description | Examples | Protection |
|----------|-------------|---------|------------|
| Fanciful | Invented words | Kodak, Xerox, Google | Strongest; immediate |
| Arbitrary | Real words, no connection | Apple (computers), Amazon | Very strong |
| Suggestive | Hints at qualities | Netflix, Greyhound | Strong; no secondary meaning needed |
| Descriptive | Describes a feature | ColdRite | Only with secondary meaning (acquired distinctiveness) |
| Generic | The name for the product | Aspirin (was a trademark) | Not protectable; mark lost |

**Strategic advice:** Choose fanciful or arbitrary marks. "FastSearch" or "EasyAI" require proving secondary meaning and are easily worked around by competitors.

### USPTO Registration Process

**Filing basis:**
- **Use in commerce (§1(a)):** Mark already in use in interstate commerce
- **Intent to Use (§1(b)):** File before use; submit Statement of Use within 36 months (extensions available)

**Process:**
1. File via USPTO Trademark Center (replaced TEAS in 2024)
2. Formal examination (8–10 months to first action)
3. Office Action response (3-month deadline, extendable for a fee)
4. Publication in Official Gazette → 30-day opposition window
5. TTAB opposition if any
6. Registration (§1(a)) or Notice of Allowance + Statement of Use (§1(b))
7. Total: 12–18 months if no complications

**Costs:**
- TEAS Plus: $250/class (most restricted form, lowest cost)
- TEAS Standard: $350/class
- Attorney fees: $1K–$3K additional

**Post-registration maintenance:**
- §8 Declaration (years 5–6)
- §15 Incontestability claim (years 5–6)
- §9 Renewal every 10 years

### Likelihood of Confusion: DuPont Factors

Primary ground for USPTO refusal and TTAB opposition. Top factors:
1. Similarity of marks (appearance, sound, meaning)
2. Relatedness of goods/services
3. Strength of the cited mark
4. Channels of trade and consumers
5. Actual confusion (not required but strong evidence)
6. Degree of purchaser care

### Trade Dress

Protects the **overall commercial image**—appearance, design, packaging. Requires:
1. Distinctiveness (inherently or acquired)
2. Non-functionality (cannot be essential to product use/purpose—*TrafFix v. Marketing Displays*, 2001)

UI/UX trade dress is powerful: Apple has litigated iPhone UI elements extensively.

---

## Trade Secrets

### What Qualifies

Under the **DTSA** (18 U.S.C. §§ 1831–1839) and **UTSA** (48 states), a trade secret must:
1. **Derive independent economic value** from not being generally known
2. Be subject to **reasonable measures** to maintain secrecy

Virtually anything qualifies if both prongs are met:
- Algorithms, model weights, training datasets, training procedures
- Customer lists, pricing strategies, financial projections
- Source code (if not publicly released)
- Hardware designs, manufacturing processes

### Reasonable Measures (The Critical Requirement)

Courts examine concrete steps:
- Mark documents "Confidential" and "Proprietary"
- Need-to-know access controls
- NDAs with employees, contractors, investors, vendors (DTSA whistleblower notice required in all)
- Technical controls: access logs, encryption, MFA
- Physical security for sensitive materials
- Exit interview protocols reminding departing employees of obligations

Passwords alone are not sufficient. Failure to maintain reasonable measures destroys trade secret status.

### DTSA vs. UTSA

| | DTSA (Federal) | UTSA (State) |
|--|----------------|-------------|
| Jurisdiction | Federal courts | State courts |
| Key remedy | **Ex parte seizure** (emergency) | Injunctive relief |
| Whistleblower immunity | Required notice in all covered agreements | Varies |
| Exemplary damages | 2× if DTSA notice included | Varies |
| Attorney fees | If DTSA notice included | Varies |

**Mandatory DTSA whistleblower notice** in ALL agreements governing trade secret use. Without it, you lose eligibility for exemplary damages and attorney fees. Template: *"An individual shall not be held criminally or civilly liable under any Federal or State trade secret law for the disclosure of a trade secret made in confidence to a Federal, State, or local government official...solely for the purpose of reporting or investigating a suspected violation of law..."*

### Non-Compete Enforceability (2025)

FTC's April 2024 rule banning non-competes was struck down in August 2024 (*Ryan LLC v. FTC*). FTC voluntarily dismissed appeals September 2025. **The rule is dead. State law governs.**

| State | Status |
|-------|--------|
| **California** | **Void and unenforceable** (§ 16600); AB 1076 requires notifying current/former employees of void non-competes |
| Minnesota | Banned Jan 1, 2023 |
| Oklahoma, North Dakota | Essentially void |
| Washington | Only if earnings > $116,593/year (2024 threshold) |
| Massachusetts | Enforceable with garden leave; max 1 year |
| Texas, Florida, Delaware | Generally enforceable if reasonable in scope, duration, geography |

California's ban applies extraterritorially—California courts won't enforce non-competes against California employees even with out-of-state choice-of-law clauses.

**Alternatives where non-competes fail:**
1. NDAs protecting specific confidential information
2. Non-solicitation of employees/customers (more widely upheld)
3. Garden leave (pay during restricted period)
4. Trade secret protections

---

## Open Source Licensing Strategy

### License Classification

**Permissive (SaaS-friendly):**

| License | Key Features |
|---------|-------------|
| **MIT** | Minimal restrictions; attribution required; no patent grant |
| **Apache 2.0** | Permissive + **explicit patent grant** + patent termination clause; preferred for enterprise (Kubernetes, TensorFlow) |
| **BSD 2/3-clause** | Very similar to MIT; 3-clause adds non-endorsement |

**Copyleft (know the risks):**

| License | Copyleft Trigger | SaaS Loophole? |
|---------|-----------------|----------------|
| **GPL v2/v3** | Distribution of derivative works | **Yes** — SaaS use without distribution doesn't trigger |
| **LGPL** | Distribution of modifications to the library itself | Generally yes for dynamically linked use |
| **AGPL v3** | Network use (running as SaaS) | **No** — AGPL closes the SaaS loophole |

**Source-Available (NOT OSI Open Source):**

| License | Who Uses It | Key Restriction |
|---------|-------------|-----------------|
| **SSPL (MongoDB)** | MongoDB (2018+) | Requires open-sourcing entire service stack if offering as SaaS |
| **BSL 1.1** | HashiCorp (Terraform, Vault), MariaDB | Non-production use only; converts to OSS after 4 years |
| **ELv2** | Elasticsearch, Kibana | Cannot provide SaaS of the software to third parties |

**Redis note:** Adopted SSPL March 2024, reversed May 2025 → returned to AGPLv3 in Redis 8.

### AGPL Risk for SaaS Companies

If your SaaS product incorporates AGPL code:
- Users have the right to receive complete corresponding source code
- Includes modifications to the AGPL component
- Does NOT require open-sourcing your entire app—only the AGPL-licensed parts

**Practical risk:** Enterprise customers flag AGPL in dependency trees. Material M&A due diligence finding. Maintain a license whitelist enforced via SCA (Software Composition Analysis) tools—FOSSA, Black Duck, Snyk License, Mend.

**License policy template:**
- Approved: MIT, Apache 2.0, BSD, ISC
- Review required: LGPL (check linking), MPL 2.0
- Prohibited in production without legal sign-off: GPL, AGPL, SSPL

### Contributor License Agreements (CLAs)

**When to use CLA:**
- You want to dual license (open source + commercial)
- You may change the project license in the future
- You need clear patent grants from contributors

**When to use DCO (Developer Certificate of Origin):**
- Lightweight contribution process
- Large community projects
- Committed to current license long-term
- DCO does NOT enable relicensing without re-contacting contributors

**CLA enforcement:** CLA-assistant, SAP/cla-assistant GitHub app. Corporate CLA (CCLA) for company employees.

### Dual Licensing Strategy

Same software available under:
1. Open source license (AGPL/GPL)—free for qualifying open source uses
2. Commercial license—required for proprietary/commercial uses

**Requirement:** You must own ALL copyright (CLA mandatory). Companies using this model: MySQL (GPL + commercial), Qt, Elastic, MongoDB.

---

## Employee & Contractor IP

### The PIIA (Proprietary Information and Inventions Agreement)

Every founder, employee, contractor, intern, and advisor must sign before doing any work.

**Core provisions:**

1. **IP Assignment (present tense):** "Contractor/Employee **hereby assigns**..." — NOT "agrees to assign." *Stanford v. Roche*, 563 U.S. 776 (2011): Future-tense "agrees to assign" does NOT automatically transfer title. Present tense is required.

2. **Confidentiality:** During and after employment; perpetual for trade secrets.

3. **Prior Invention Exclusion:** Employee lists pre-existing IP to exclude—prevents disputes over pre-employment work.

4. **DTSA Whistleblower Notice:** Mandatory immunity language (see Trade Secrets section).

5. **Works for Hire + Assignment backup:** Both belt and suspenders.

6. **Non-solicitation of employees/customers:** More widely enforceable than non-competes.

### California Labor Code § 2870 Carve-Out

California (and similar laws in Delaware, IL, MN, NC, WA) limits IP assignment scope. Employers **cannot** require assignment of inventions:
- Developed entirely on employee's **own time**
- Without using **employer's equipment, supplies, facilities, or trade secrets**
- That do **not** relate to employer's actual or reasonably anticipated business/R&D
- That do **not** result from work performed for employer

Every California PIIA must include § 2870 exclusion language. Omitting it makes the entire assignment clause unenforceable.

### Founder IP Considerations

**Pre-incorporation code:** Code written before incorporation belongs to founders personally. Execute a Founders' IP Assignment Agreement at or before incorporation.

**University tech transfer:** Founders who developed technology at universities must review employment/student agreements. The **Bayh-Dole Act** (35 U.S.C. §§ 200–212) gives US universities the right to retain ownership of federally funded inventions. Negotiate and document licensing agreements with the university TTO before founding.

**Prior employer agreements:** Review former employer's PIIA for scope of IP assignment, garden leave, and trade secret obligations.

---

## International IP

### PCT Applications

PCT covers **158 states**. One application, deferred national phase entries.

**Timeline:**
- Month 0: File PCT (claiming priority from provisional if filed within 12 months)
- Month 18: Publication
- Month 16–28: International Search Report + Written Opinion
- **Month 30: National phase entry deadline** (per most countries)

**Costs:**
- International filing fee: CHF 1,400 (~$1,550)
- Search fee (USPTO as ISA): ~$2,100
- National phase per country: $5K–$10K in attorney + filing fees
- Small entity: 60% discount; micro entity: 80% discount on USPTO fees

**PCT vs. direct filing:** PCT defers expensive national filings by 18+ months. Provides ISR previewing examination quality. Use PCT when you need market validation before committing to national filings. Direct filing is faster if you know exactly which countries within the first 12 months.

### European Patent System + UPC

**Unified Patent Court (UPC)** — in force June 2023:
- 18+ EU member states participating (as of mid-2025)
- **Unitary Patent:** Single registration across all UPC states for ~€5K–€6K (vs. €36K+ for national validation in 5 countries)
- **Centralized risk:** A single UPC revocation decision invalidates the patent across all states simultaneously
- **Opt-out:** Patent owners can opt EP patents OUT of UPC jurisdiction during transitional period—evaluate case-by-case

### China IP Strategy

- **First-to-file** (patents and trademarks)—file early
- CNIPA: 1.8M patent applications in 2025; 77 national IP protection centers; fast-track AI patent examination
- **Register trademarks BEFORE entering the Chinese market**—China first-to-file trademark system; notorious mark protection is limited
- Trade secret protection: Chinese courts enforce; document "reasonable measures" as in the US
- Utility model patents: Shorter term, less scrutiny, granted faster—use alongside invention patents for time-sensitive technology

### Madrid Protocol (International Trademarks)

Covers **132 countries**. One application through home IP office (USPTO for US filers).

**Fee structure:**
- Basic fee: CHF 653 (black/white) or CHF 903 (color)
- Supplementary fee: CHF 100/class beyond 3
- Filing in 5 key countries for 3-class mark: ~$1,250–$3,000

**Strategic priority markets:** US, EU (EUTM covers 27 EU countries via EUIPO), UK (separate post-Brexit), Canada, Australia, Japan, China (file separately for best results), India.

---

## IP Due Diligence

### What Investors Look For

At every funding round and in M&A:

**Ownership and chain of title:**
- All founders/employees/contractors assigned IP to company
- Pre-incorporation code assigned
- No university or prior employer entanglements
- All assignments recorded at USPTO for patents

**IP audit checklist:**
- All patents (issued, pending, provisional, continuation)
- Trademark registrations (domestic and international)
- Domain names
- Software copyright registrations
- Trade secret protection program documentation
- License agreements in and out
- Open source SCA audit
- FTO analysis for core product features

### Common Red Flags

1. **Founder IP not assigned:** Pre-incorporation code in founders' personal names
2. **Missing contractor assignments:** Offshore dev shop built core product without assignment
3. **Unregistered trademarks:** Similar marks exist; brand unprotected
4. **GPL/AGPL contamination:** GPL code in proprietary product requiring release
5. **No FTO analysis:** Operating in patent-dense space without clearance
6. **University IP agreement not reviewed:** Bayh-Dole obligations
7. **No DTSA notice in employment agreements:** Eliminates punitive damages + attorney fees
8. **"Agrees to assign" language:** Not automatic assignment; invalidated by Stanford v. Roche

### IP Reps & Warranties in Transactions

Standard representations:
- Company owns all IP used in the business; no third-party rights
- Products/services do not infringe third-party IP
- No pending/threatened IP litigation
- All employees/contractors have assigned IP
- No open source incorporated in manner requiring release of proprietary code
- Reasonable measures to maintain trade secrets

**AI-specific reps (2024 market trend):**
- AI systems comply with applicable laws
- Training data was lawfully obtained
- No known bias issues
- No pending AI regulatory investigations

**R&W Insurance (Representations & Warranties Insurance):** Standard in deals >$50M; increasingly used in smaller deals. Seller gets near-full escrow release at close; buyer has direct claim against insurer.

---

## IP Litigation

### Patent Trolls (NPEs)

NPE cases increased ~15–20% Q1→Q2 2025. Most targeted: software, e-commerce, fintech, mobile, wireless.

**NPE playbook:** Acquire broad pre-Alice patents → File in friendly jurisdiction (W.D. Texas, D. Delaware) → Demand nuisance-value settlement ($50K–$500K < cost of litigation).

**Defense strategies:**
1. **IPR (Inter Partes Review):** File at PTAB within 1 year of being served; challenge §102/§103 validity. Filing fee: $19,500 (up to 20 claims). Institution rate ~50% in FY2025 (declined; discretionary denial increased)
2. **Alice motion:** File early §101 motion to dismiss—many NPE patents are broad pre-Alice claims
3. **Transfer to home district:** Seek transfer out of W.D. Texas per *In re Apple Inc.* (Fed. Cir. 2021)
4. **Unified Patents:** Subscription service that challenges NPE patents through IPR
5. **LOT Network / Open Invention Network:** Defensive patent pools; licenses to patents transferred to NPEs

### TTAB (Trademark Trial and Appeal Board)

Handles trademark opposition and cancellation—more cost-effective than federal court.

**Opposition:** 30 days from publication in Official Gazette (extendable).

**Cancellation:** Petition against existing registration; must have standing.

**TTAB vs. Federal Court:** TTAB can only cancel registrations / block new ones—no damages. Federal court: injunctions, damages, attorney fees. TTAB findings on likelihood of confusion have preclusive effect in subsequent federal court proceedings.

---

## IP Strategy Matrix for AI Startups

| Asset | Primary Protection | Secondary |
|-------|-------------------|-----------|
| Training data | Trade secret + license agreements | Contracts |
| Model weights | Trade secret | Patent (if novel architecture) |
| Training algorithms | Patent + trade secret | — |
| Model architecture | Patent + trade secret | — |
| Fine-tuning datasets | Trade secret + contracts | — |
| Inference optimization | Patent + trade secret | — |
| API/SDK | Copyright (code) + trade dress (interface) | — |
| Generated outputs | Copyright (where human authorship present) | Contracts |
| Brand | Trademark | Trade dress |

---

## Startup IP Action List

### Day 1 / Pre-Launch
- [ ] All founders and employees sign PIIA ("hereby assigns," § 2870 carve-out if CA, DTSA notice)
- [ ] All contractors sign IP assignment agreements
- [ ] Trademark clearance search for company name/brand
- [ ] File provisional patent application(s) on core technology
- [ ] Open source dependency audit
- [ ] Review prior employer agreements for founders

### Seed Round
- [ ] File trademark applications at USPTO
- [ ] Convert provisional to non-provisional within 12 months
- [ ] FTO analysis on core product features
- [ ] Register copyright in key software
- [ ] Trade secret protection program (NDAs, access controls, policies)
- [ ] File PCT if international markets matter

### Series A Due Diligence
- [ ] IP data room (all assignments, prosecution histories, registrations)
- [ ] Confirm all assignments recorded at USPTO
- [ ] Comprehensive open source license audit
- [ ] IP schedule ready (list of all owned, licensed, pending IP)

### Ongoing
- [ ] Trademark watch services
- [ ] Continuation applications as product evolves
- [ ] Open source compliance program
- [ ] Trademark renewals
- [ ] Annual IP audit

---

## Key Cases Quick Reference

| Case | Holding |
|------|---------|
| *Alice Corp. v. CLS Bank*, 573 U.S. 208 (2014) | Two-step §101 test; abstract idea + generic computer = ineligible |
| *Recentive Analytics v. Fox Corp.*, 134 F.4th 1205 (Fed. Cir. 2025) | Applying existing ML to new domain without improving ML process = ineligible |
| *Thaler v. Vidal*, 43 F.4th 1207 (Fed. Cir. 2022) | AI cannot be named as inventor |
| *Thaler v. Perlmutter* (D.C. Cir. 2025, cert denied 2026) | AI-generated works without human authorship not copyrightable |
| *Thomson Reuters v. Ross Intelligence* (D. Del. Feb. 2025) | Training AI on copyrighted works is not fair use where market harm exists |
| *Stanford v. Roche*, 563 U.S. 776 (2011) | "Agrees to assign" ≠ automatic assignment; present tense required |
| *Feist v. Rural Telephone*, 499 U.S. 340 (1991) | Mere compilation of facts not copyrightable; requires originality |
| *Oracle v. Google*, 141 S. Ct. 1183 (2021) | Copying Java API declarations was fair use on specific facts |
| *Jacobsen v. Katzer*, 535 F.3d 1373 (Fed. Cir. 2008) | Open source licenses are enforceable copyright licenses |
