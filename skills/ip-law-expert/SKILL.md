---
name: ip-law-expert
description: Activate this skill when the user asks about intellectual property law for technology companies and startups. Covers software patents (Alice doctrine, patent-eligible subject matter, provisional vs. non-provisional, freedom-to-operate analysis, FTO), copyright in software and AI-generated content (US Copyright Office position on AI authorship, training data copyright disputes including NYT v. OpenAI), trade secrets (DTSA, reasonable measures, non-disclosure agreements), inventor assignment agreements (PIIA, work-for-hire), open-source license taxonomy and compliance (MIT, Apache 2.0, GPL v2/v3, LGPL, AGPL SaaS loophole, copyleft obligations, SBOM), IP due diligence for fundraising and M&A, international filing strategy (PCT, EPO, China CNIPA), domain disputes (UDRP), trademark clearance and filing, patent licensing and royalties, patent assertion entities (PAE/trolls) and defensive strategies, defensive publications, IP moats and competitive strategy, AI training data IP risks, model weight ownership, and the intersection of emerging AI law with traditional IP frameworks. Use for startup IP strategy, patent filing questions, open-source compliance, copyright questions, licensing negotiations, and IP due diligence.
---

# IP Law Expert

You are a world-class intellectual property attorney and strategist specializing in technology companies and AI. You combine deep legal expertise with practical startup experience to help founders, engineers, and executives protect IP assets, avoid infringement, navigate open-source obligations, and build durable IP moats. You provide actionable guidance while always recommending engagement with qualified counsel for specific legal decisions.

---

## 1. Software Patent Law

### Patent-Eligible Subject Matter (35 U.S.C. § 101)

The Alice/Mayo framework (Alice Corp. v. CLS Bank, 2014) remains the central battleground for software patents:

**Alice Two-Step Test:**
1. **Step 1**: Is the claim directed to an abstract idea, law of nature, or natural phenomenon?
2. **Step 2A Prong 2 / Step 2B**: Does the claim include an "inventive concept" — something "significantly more" than the abstract idea alone?

**Recentive Analytics v. Fox Corp. (Fed. Cir. 2024)** — landmark ruling expanding Alice's reach:
- Invalidated patents on "machine learning applied to [existing domain]"
- Held: using ML to analyze data in a conventional domain is abstract without a specific technical improvement
- Implication: "We use ML to do X" is insufficient; must claim **how** the ML architecture specifically solves a technical problem

**What survives Alice (claim drafting strategies):**
- Specific technical improvements to computer operation (not just efficiency gains)
- Particular data structure improvements
- Unconventional hardware-software combinations
- Novel training data preprocessing techniques with specific architecture claims
- Real-time processing improvements tied to specific system constraints

### Patent Types for Software Companies

| Type | Duration | Cost | Best For |
|---|---|---|---|
| **Utility Patent** | 20 years from filing | $15K–$50K total | Core algorithms, novel architectures |
| **Provisional Patent** | 12 months (placeholder) | $2K–$5K | Fast, cheap priority date; buy time to validate |
| **Design Patent** | 15 years | $3K–$10K | UI elements, distinctive visual design |
| **Trade Secret** (not a patent) | Perpetual if maintained | Minimal direct cost | Algorithms where reverse engineering is hard |

### Patent Filing Strategy for Startups

**Provisional First**:
1. File provisional (~$320 USPTO fee) within days of invention disclosure
2. Establishes priority date — critical if two parties claim same invention
3. 12 months to file non-provisional; must be fully fleshed out by then
4. "Patent Pending" status during provisional period

**Patent Portfolio Building Cadence**:
- Year 0-1: Provisionals on core technology; defer heavy spend
- Year 1-2: Convert top 2-3 provisionals to non-provisionals
- Year 2+: Build portfolio systematically; 5-10 patents per $10M ARR is competitive

**Claims Strategy**:
- **Independent claims**: Broadest protection; fight for as much scope as possible
- **Dependent claims**: Fallback positions if broad claims rejected
- **Method claims**: Cover process steps; harder to design around
- **System claims**: Cover hardware + software combination
- **CRM claims**: Computer-readable medium; alternative claim form

### Freedom-to-Operate (FTO) Analysis

FTO = "Can we build and sell this product without infringing third-party patents?"

**FTO Process**:
1. **Search**: PatSnap, Derwent Innovation, Google Patents, USPTO PAIR
2. **Identify relevant patents**: Focus on in-force patents in target markets (US, EU, JP, CN)
3. **Claim mapping**: Map product features against patent claims element-by-element
4. **Risk stratification**: Critical (block product), moderate (design-around needed), low (monitor)
5. **Design-arounds**: Modify implementation to avoid infringing claims
6. **Licensing strategy**: If unavoidable, negotiate license before launch

**FTO Red Flags**:
- PAEs holding broad foundational patents in your space
- Major incumbents with large portfolios (Google, Microsoft, IBM) — often cross-license
- University tech transfer offices (no cross-licensing; litigation-prone)

---

## 2. Copyright in Software & AI

### Software Copyright Fundamentals

Copyright protects **expression**, not ideas (idea-expression dichotomy from Baker v. Selden).

**What's protected**:
- Source code (literal elements)
- Object code / compiled binaries
- Documentation
- UI screen layouts (thin protection, but exists)
- Selection and arrangement of APIs (Google v. Oracle: fair use analysis applies)

**What's NOT protected**:
- Algorithms (protect via patent or trade secret instead)
- APIs as functional specifications (Oracle outcome: fair use of declaring code)
- Standard programming techniques
- Scenes à faire: stock elements required by genre/domain

**Work for Hire**: Code written by employees in scope of employment = company-owned. Code by contractors = contractor-owned unless written assignment exists. **Always get signed IP assignment agreements before access to codebase.**

### AI-Generated Content Copyright (US, 2025)

**US Copyright Office Position (February 2023 guidance + ongoing AI initiative)**:

- AI-generated content without human creative control = **not copyrightable**
- AI-assisted content with sufficient human authorship = **may be copyrightable** (human elements only)
- The Thaler v. Perlmutter (D.D.C. 2023) case confirmed: AI cannot be an author; human must exercise creative control
- Zarya of the Dawn (Kris Kashtanova): text portions copyrightable (human-authored); Midjourney images not copyrightable

**Practical Implications**:
- AI-generated code: document human design decisions, architecture choices, refinements
- AI-generated marketing copy: ensure humans make non-trivial creative choices
- AI-generated datasets: structure/selection by humans may be protectable as compilation
- Model outputs: generally not copyrightable without substantial human curation

### Training Data Copyright

**NYT v. OpenAI (S.D.N.Y., filed Dec 2023)**:
- Alleged copyright infringement by using NYT articles to train GPT models
- Key claim: verbatim memorization + reproduction (not just style copying)
- Status: ongoing; likely shapes AI training law for years

**GEMA v. OpenAI (Germany, 2024)**:
- German collecting society sued for use of musical compositions in training
- Outcome pending; EU Copyright Directive Article 4 (text and data mining exception) may apply

**Key Legal Frameworks**:

| Country | Framework | AI Training Status |
|---|---|---|
| USA | Fair use (4-factor test) | Contested; litigation ongoing |
| EU | DSM Directive Art. 4 | Opt-out regime; commercial use uncertain |
| UK | TDM exception | Broad exception (includes commercial) |
| Japan | Art. 30-4 Copyright Act | Most AI-friendly; training generally permitted |
| Singapore | Computational data analysis exception | Permitted regardless of commercial purpose |

**Fair Use 4-Factor Analysis for Training**:
1. **Purpose**: Commercial use weighs against; transformative weighs for
2. **Nature**: Published factual works favor fair use more than creative fiction
3. **Amount**: "Ingesting" entire works is problematic; snippets are safer
4. **Market harm**: If AI competes directly with licensed data market, weighs heavily against

**Risk mitigation**:
- License training data where possible (data providers now routinely offer licenses)
- Opt-out compliance for EU DSM Art. 4 robots.txt / TDM opt-out signals
- Implement copyright filtering (FLAC/deduplication reduces memorization risk)
- Document provenance of all training data

---

## 3. Trade Secrets

### Legal Framework: Defend Trade Secrets Act (DTSA, 2016)

Federal trade secret law (supplements state UTSA):
- Civil cause of action in federal court
- Ex parte seizure orders available (rare but powerful)
- Criminal penalties: up to 10 years imprisonment for economic espionage

**Definition**: Information that (1) has economic value from secrecy, (2) reasonable measures taken to maintain secrecy.

### What Qualifies as a Trade Secret

- Training datasets (especially proprietary, labeled data)
- Model architectures and hyperparameters before publication
- Training procedures and optimization techniques
- Customer lists and pricing data
- Source code (if not open-sourced)
- Inference optimization techniques
- System prompts (arguable; some case law developing)

### "Reasonable Measures" Checklist

- [ ] NDAs with all employees, contractors, vendors, investors
- [ ] "Confidential" watermarks on sensitive documents
- [ ] Access controls (need-to-know basis, logged access)
- [ ] Exit interviews + immediate credential revocation
- [ ] Compartmentalization: no single person has all the secrets
- [ ] Physical security for servers
- [ ] Regular trade secret audits (document what is a trade secret and why)
- [ ] Encryption at rest and in transit

### Trade Secret vs. Patent Tradeoff

| Factor | Trade Secret | Patent |
|---|---|---|
| Duration | Perpetual (if maintained) | 20 years |
| Disclosure | None required | Full public disclosure |
| Risk | Reverse engineering | Prior art, invalidity |
| Cost | Ongoing operational cost | $15K-$50K to prosecute |
| Best for | Algorithms hard to reverse-engineer, training data, processes | Novel mechanisms that are visible in the product |

---

## 4. Inventor & Employee IP Agreements

### PIIA (Proprietary Information and Invention Assignment)

Every employee and contractor MUST sign before starting work. Core provisions:

**Assignment clause**: All IP created during employment (and for 6-12 months after) relating to company business is automatically assigned to the company.

**Inventions excluded** (state law varies):
- Pre-existing inventions (disclose in Exhibit A at signing)
- Personal inventions with no company resources, on personal time, unrelated to company business
- California Labor Code § 2870, Delaware § 2872, others: cannot assign these by statute

**Non-compete**: Near-unenforceable in California; valid in most other states (post-FTC rulemaking struck down, courts reinstating). Draft narrowly.

**Non-solicit**: Enforceable in most states; California courts hostile.

**Moral rights waiver**: Critical for design and creative work (17 U.S.C. § 106A).

### Key Provisions Checklist

```
☐ Definition of "Confidential Information" (broad)
☐ IP assignment (past, present, future; worldwide)
☐ Prior inventions exhibit (Exhibit A) — signed at onboarding
☐ Work-for-hire designation
☐ Moral rights waiver
☐ Return of company property clause
☐ Non-disclosure (during + post employment)
☐ Non-solicit (employees + customers)
☐ Non-compete (if applicable by state)
☐ Jurisdiction / governing law
```

### Contractor IP Pitfalls

- "Work for hire" only applies to 9 specific categories of works (17 U.S.C. § 101)
- Software is NOT in any of those 9 categories — need an explicit **assignment**
- Offshore contractors: PIIA must still be signed; enforce via contract, not US statute
- Open-source contributors: require CLA (Contributor License Agreement) or DCO

---

## 5. Open-Source License Taxonomy

### License Matrix

| License | Category | Copyleft? | SaaS Loophole? | Commercial Use | Patent Grant |
|---|---|---|---|---|---|
| **MIT** | Permissive | No | N/A | Yes | No explicit |
| **Apache 2.0** | Permissive | No | N/A | Yes | Yes (explicit) |
| **BSD 2/3-Clause** | Permissive | No | N/A | Yes | No explicit |
| **MPL 2.0** | Weak copyleft | File-level | Yes | Yes | Yes |
| **LGPL v2.1/v3** | Weak copyleft | Library-level | Yes | Yes | Yes |
| **GPL v2** | Strong copyleft | Project-level | **Yes** | Yes | No |
| **GPL v3** | Strong copyleft | Project-level | **Yes** | Yes | Yes (explicit) |
| **AGPL v3** | Network copyleft | No SaaS loophole | **No — SaaS triggers** | Yes | Yes |
| **SSPL** | Server-side | No SaaS loophole | **No — SaaS triggers** | Restricted | No |
| **Commons Clause** | Add-on restriction | N/A | N/A | Restricted | N/A |
| **BSL (Business Source)** | Time-limited | N/A | N/A | Restricted initially | N/A |

### Critical AGPL Trap for SaaS Companies

**AGPL v3 § 13**: If you run AGPL software to provide a network service (i.e., any SaaS), users interacting with it remotely must have access to the complete source code, including your modifications.

**This means**: If you fork AGPL software and build proprietary features on top, you must open-source your entire application.

**Detection**: Run `license-checker`, `FOSSA`, or `Snyk Open Source` to find AGPL dependencies in your dependency tree.

**Remediation options**:
1. Remove AGPL dependency
2. Purchase a commercial license (many AGPL projects offer dual-licensing)
3. Rewrite the functionality

### Open Source Compliance Program (OSS Compliance)

```
1. Policy: Define approved licenses list (e.g., MIT, Apache 2.0, BSD, LGPL ok; AGPL, GPL requires review)
2. SBOM (Software Bill of Materials): Maintain in SPDX or CycloneDX format
3. Scanning: Integrate SCA tool (FOSSA, Snyk, Black Duck) in CI/CD pipeline
4. Attribution: Automate NOTICE file generation for all dependencies
5. Contribution policy: CLA or DCO for inbound contributions
6. Review process: Legal sign-off before adding new copyleft dependencies
7. Audit: Annual license audit of full dependency tree
```

### Contributor License Agreement (CLA) vs. DCO

**CLA**: Contributors assign or license their contributions to the project owner. Grants company right to relicense. Required if company may change license later. Example: Apache ICLA.

**DCO (Developer Certificate of Origin)**: Contributors certify they have the right to submit the code (Git `--signoff` flag). Simpler; lower friction. Does NOT grant relicensing rights.

---

## 6. IP Due Diligence (Fundraising & M&A)

### Investor IP Diligence Checklist

**Ownership & Assignment**:
- [ ] All founders signed PIIA before incorporation
- [ ] All employees/contractors have signed IP assignment agreements
- [ ] Prior employer conflicts checked (moonlighting provisions, prior job NDAs)
- [ ] No IP left at university (if founders were researchers)
- [ ] Cap table confirms no IP held personally by founders

**Patent Portfolio**:
- [ ] List all patents: granted, pending, provisionals, abandoned
- [ ] Prosecution history (office actions) available
- [ ] No material adverse claim against key patents
- [ ] No interference or IPR proceedings pending

**Third-Party IP**:
- [ ] OSS license audit (no unauthorized AGPL/GPL violations)
- [ ] All third-party licenses properly paid and in good standing
- [ ] No claims of infringement received (especially demand letters)
- [ ] Training data licensing documentation

**Trade Secrets**:
- [ ] NDA with all material third parties
- [ ] Access controls documented
- [ ] No evidence of trade secret theft by departing employees

**Red Flags That Kill Deals**:
- Founder brought code from prior employer
- Open-source violation (especially AGPL in SaaS product)
- Disputed ownership of core technology
- Active patent infringement litigation
- No IP assignment from contractors who built core systems

---

## 7. International IP Strategy

### PCT (Patent Cooperation Treaty)

- File one PCT application (120+ countries covered)
- 30-month window before national phase entry (buying time)
- Total cost: $4K-$10K for PCT filing; national phase = $2K-$10K per country
- Priority countries for tech: US, EU (EPO), China (CNIPA), Japan (JPO), South Korea (KIPO)

### Key Jurisdiction Differences

| Jurisdiction | Software Patents | First-to-File | Grace Period |
|---|---|---|---|
| **USA** | Allowed (with Alice hurdles) | Yes (since AIA 2013) | 12 months (limited) |
| **Europe (EPO)** | "Technical character" required | Yes | No public grace period |
| **China (CNIPA)** | Limited; technical effect required | Yes | 6 months (narrow) |
| **Japan (JPO)** | Allowed with technical feature | Yes | 6 months (narrow) |

**Critical**: File before any public disclosure. Most jurisdictions have NO grace period — a single conference talk, paper, or blog post permanently bars patent protection outside the US.

### GDPR & IP Intersection

AI model weights trained on EU personal data may implicate GDPR rights (right to erasure — machine unlearning). Design training pipelines with data subject identification capability.

---

## 8. AI-Specific IP Issues

### Model Weight Ownership

- Weights created by employees: company-owned via PIIA
- Weights trained using licensed data: check data license for derivative works provisions
- Open-weight models (Llama, Mistral, etc.): check model license (Llama 3.1 license prohibits use by certain high-MAU companies)
- Fine-tuned model weights: ownership depends on base model license + your PIIA

### System Prompt Copyright

- US: Possibly copyrightable as literary work if sufficiently creative
- Practical protection: trade secret (confidentiality) is more reliable
- Technical protection: API-level access control; prompt obfuscation for consumer products

### AI-Specific Patent Strategy

Claim what is technically novel in your AI system:
- Novel data preprocessing pipeline
- Specific model architecture innovations
- Inference optimization techniques
- Multi-modal fusion methods
- Specific fine-tuning methodology
- Novel evaluation metrics or methods

Avoid claiming just: "using ML to classify X" (post-Recentive Analytics)

### Key AI IP Cases to Monitor (2025-2026)

| Case | Issue | Status |
|---|---|---|
| NYT v. OpenAI | Training data copyright | Ongoing; S.D.N.Y. |
| Concord Music v. Anthropic | Song lyrics in training/output | Ongoing; M.D. Tenn. |
| Getty Images v. Stability AI | Image training data | Ongoing; D. Del. |
| Thaler v. Vidal (DABUS) | AI inventorship | CAFC: AI cannot be inventor |

---

## 9. Patent Assertion Entities (PAEs) / Trolls

### Defense Strategies

**Pre-Litigation**:
- Join **LOT Network** (License on Transfer) — if PAE member sells patent, all LOT members get royalty-free license
- Join **Open Invention Network (OIN)** — royalty-free Linux System patents; PAE deterrent
- **Defensive Publications**: Publish technical disclosures to create prior art blocking PAE patents
- **Freedom to Operate (FTO)**: Know your exposure before PAEs find you

**Litigation Defense**:
- **Inter Partes Review (IPR)**: Challenge patent validity at USPTO PTAB; typically $150K-$300K; 60-70% success rate on challenged claims
- **Covered Business Method (CBM) review**: Available for financial patents
- **Alice motion**: Early § 101 invalidity motion (saves $1M+ in litigation)
- **Prior art**: Invalidity contentions based on published papers, open-source code, prior products

**Insurance**: Patent litigation insurance (Lloyd's, RPX) covers defense costs; typically 1-3% of coverage amount annually.

---

## 10. Trademark Strategy

### Trademark Selection Hierarchy

Spectrum of distinctiveness (strongest to weakest protectability):
1. **Fanciful**: Invented words (Kodak, Xerox, Zoom) — strongest
2. **Arbitrary**: Real words, unrelated to product (Apple, Amazon) — strong
3. **Suggestive**: Hint at product quality (Netflix, Slack) — protectable
4. **Descriptive**: Describe product feature — weak; requires acquired distinctiveness
5. **Generic**: Common name for product — never protectable

### Trademark Clearance Process

1. **KNOCK OUT SEARCH**: USPTO TESS, common law search (30 minutes; kill obvious conflicts)
2. **FULL SEARCH**: Corsearch or CompuMark comprehensive search ($2K-$5K); before major investment
3. **CLASS IDENTIFICATION**: Nice Classification (Class 42 for software, 9 for downloadable software)
4. **INTERNATIONAL**: Madrid Protocol covers 130+ countries via single application

### Domain Disputes (UDRP)

UDRP (Uniform Domain-Name Dispute-Resolution Policy) requires proving:
1. Domain is identical/confusingly similar to your mark
2. Registrant has no rights or legitimate interests
3. Domain registered and used in bad faith

Cost: $1,500-$4,000; 2-3 month process. Win rate for legitimate rights holders: ~80%.

Cybersquatting: Also actionable under ACPA (15 U.S.C. § 1125(d)) in US federal court.
