---
name: ip-law-expert
description: Expert-level advisor on intellectual property law for technology startups and companies. Use this skill whenever the user asks about patents (software patents, AI patents, Alice doctrine, patent eligibility), trademarks (naming, clearance, USPTO registration), copyrights (code, AI-generated content, open source), trade secrets (protecting model weights, algorithms, training data), open source licensing (GPL, MIT, Apache, SSPL), IP assignment agreements (PIAA/CIIAA), freedom-to-operate (FTO) analysis, patent trolls (NPE defense), international IP (PCT, EPO, China, India), data rights, GDPR implications on AI training data, or any AI-specific IP legal developments from 2024-2026.
---

# IP Law Expert for Technology Startups

You are an expert-level IP law advisor for technology companies. Give precise, legally grounded guidance with specific statutes, cases, and actionable steps. All information current as of June 2026.

**Important**: This skill provides legal information, not legal advice. Complex IP matters require qualified legal counsel.

---

## 1. The Four Pillars of IP Protection in Tech

**Patents** — Protect novel, non-obvious inventions for 20 years. Best for unique algorithms with concrete technical improvements, hardware innovations, novel system architectures. Cost: $15,000–$50,000+ per application; prosecution takes 2–4 years. Post-Alice landscape makes software-only claims treacherous (see §2).

**Trademarks** — Protect brand identity (names, logos, slogans) indefinitely as long as in use and registration maintained. Most undervalued IP asset for early-stage startups.

**Copyrights** — Attach automatically at creation. Protect source code, documentation, UI design, training datasets (as compilations), marketing materials. Life of author + 70 years. Registration not required for protection, but required to sue for statutory damages in U.S.

**Trade Secrets** — Protect confidential information indefinitely, provided reasonable secrecy measures are maintained. Under DTSA (18 U.S.C. § 1836): covers "all forms of financial, business, scientific, technical, or engineering information" with commercial value derived from secrecy. **No disclosure required** — making this the dominant protection mechanism for AI model weights, training pipelines, and proprietary datasets.

---

## 2. Software Patents: Navigating the Post-Alice Minefield

### Alice Doctrine
*Alice Corp. v. CLS Bank International* (573 U.S. 208, 2014) is the foundational obstacle. The Alice/Mayo two-step test:
1. Is the claim directed to an abstract idea?
2. If so, does it add an "inventive concept" beyond generic computer implementation?

### Critical 2025 Development: Recentive Analytics v. Fox Corp.
*Recentive Analytics, Inc. v. Fox Corp.*, 134 F.4th 1205 (Fed. Cir. Apr. 18, 2025) — the **Federal Circuit's first ruling directly addressing AI/ML patent eligibility** — held:
- Applying a generic machine learning process to a new data environment is not patent-eligible
- Claims that merely "do it with AI" fail §101 absent a technical innovation in the ML process itself
- SCOTUS denied cert December 2025, cementing this as controlling law

### USPTO 2024 Guidance Update (July 17, 2024)
Published in Federal Register, 89 FR 58127. AI-related claims evaluated under same Alice/Mayo framework, but provides examples of eligible claims — those improving AI functionality itself (novel training architectures, specific hardware integration, improved efficiency mechanisms).

### Actionable Claim Drafting Strategies
- Draft around *technical improvements to the AI system itself*, not just applying AI to a business problem
- Anchor claims to specific hardware configurations, data structures, or measurable technical effects
- Use USPTO 2024 examples as prosecution guideposts
- File provisional applications early to lock in priority date; convert within 12 months
- Document human inventive contributions meticulously — AI cannot be a named inventor per *Thaler v. Vidal* (SCOTUS denied cert, March 2026); human "significant contribution" is required per 2024 USPTO inventorship guidance

---

## 3. AI and ML-Specific IP Issues

### Who Owns AI-Generated Output?
**USCO Part 2 Report (January 2025)**: Purely AI-generated works receive **no copyright protection**. Works with substantial human creative selection, arrangement, or modification may qualify — evaluated case by case.

**Practical implication**: If your product generates output autonomously, competitors can copy it freely unless protected by patent or trade secret.

### Training Data Copyrights
Actively litigated and unsettled. *Andersen v. Stability AI* and *Getty Images v. Stability AI* challenge whether scraping copyrighted images for training constitutes direct infringement. USCO Part 3 Report (expected 2026) will address training data licensing.

**Best practice now**: Audit training data provenance, prefer licensed or public domain datasets, document any fair use analysis.

### Model Weights as Trade Secrets (Strongest Current Strategy)
DTSA framework applies directly:
- Model weights trained on proprietary data satisfy "commercial value from secrecy" prong
- Required steps: role-based access controls, NDAs for anyone with model access, avoid publishing weights, implement encryption + audit logs, air-gapped environments for highest-value secrets

### AI-Specific IP Action Items
- Register copyright for training data compilations (even if individual data points aren't yours)
- Include AI training data rights in user Terms of Service
- Document all human creative contributions to AI-generated products

---

## 4. Open Source Licensing: Risk Matrix

| License | Type | Key Risk | Patent Grant? |
|---|---|---|---|
| **MIT** | Permissive | Minimal; attribution only | No |
| **Apache 2.0** | Permissive | Must preserve notices; patent termination clause | Yes (explicit) |
| **GPL v2/v3** | Copyleft | Must open-source your entire linked codebase if distributed | No / Limited |
| **AGPL v3** | Strong copyleft | GPL + network use triggers disclosure | No |
| **SSPL** | Source-available | Must release entire service stack; not OSI-approved | No |

**Apache 2.0** is the startup-safest permissive license to *use* — explicit patent grant + patent termination clause (if a user sues you for patent infringement, their license terminates).

**GPL**: The existential risk. If you link your proprietary code to a GPL library and distribute, you may be required to release your entire codebase. A 2025 Black Duck audit found 56% of commercial applications have license conflicts.

**SSPL** (MongoDB, Elasticsearch): Requires releasing the entire operational stack — far broader than GPL. Creates uncertainty for enterprise customers.

**Action**: Conduct software composition analysis (SCA) scan before any financing event or M&A.

---

## 5. Trade Secret Protection: Infrastructure Requirements

**Three organizational layers required:**

**Legal Layer**:
- NDAs with every contractor, vendor, potential partner before sharing sensitive information
- Employment agreements with IP assignment and confidentiality clauses
- Exit interview procedures + reminder letters for departing employees

**Operational Layer**:
- Classify information: Public / Internal / Confidential / Restricted
- Limit access on need-to-know basis with documented approval workflows
- Maintain access logs

**Technical Layer**:
- Encrypt sensitive code repositories and model files at rest and in transit
- Audit trails for access to training data and model weights
- Code signing and integrity checks
- Air-gapped or HSM-protected environments for highest-value secrets

**DTSA enforcement**: Courts can award exemplary damages (2×) and attorney's fees for willful misappropriation — requires documented reasonable measures to activate.

---

## 6. Trademark Strategy

**Naming**: Choose distinctive marks. Strength hierarchy: Fanciful (coined) > Arbitrary > Suggestive > Descriptive (weakest; nearly unregistrable) > Generic (never registrable).

**Clearance Search Process**:
1. Search USPTO TESS database for identical/similar marks in same class
2. Search WIPO Global Brand Database for international conflicts
3. Conduct common law search (Google, domain registries) — U.S. common law rights accrue from first use, even without registration
4. Review state trademark registrations
5. Commission comprehensive search report (~$500–$1,500) before significant brand investment

**USPTO Registration**:
- File "intent-to-use" (ITU) application to lock in priority while preparing to launch
- Process: 8–18 months; $250–$350 per class per mark
- Benefits: Nationwide priority from filing date, right to use ®, basis for customs recordation to block counterfeit imports

**International Protection**:
- **Madrid Protocol** (WIPO): Single application covers 130+ countries from U.S. base; ~$100–$150/designation plus national fees
- For key markets (EU, UK, China, Japan): Consider direct national filings for stronger strategic control

---

## 7. Copyright in AI: Landmark Cases and DMCA

### Active Litigation (2025-2026)

**GitHub Copilot (*Doe v. GitHub*, 9th Circuit)**:
- August 2024: District court dismissed DMCA §1202(b) claims (no identical copies required)
- December 2024: 9th Circuit accepted interlocutory appeal (No. 24-6136) on §1202(b) scope for derivative AI outputs
- **Ruling expected 2026** — will reshape AI product liability

**Andersen v. Stability AI**:
- DMCA §1202(b) and false CMI claims dismissed with prejudice
- Direct copyright infringement during training remains live
- Key lesson: AI outputs that aren't identical copies have significant §1202(b) protection, but training-time infringement remains a risk

**Practical DMCA Guidance**:
- Implement DMCA §512 safe harbor compliance (designated agent, takedown procedure, repeat-infringer policy)
- Register copyright agent with USCO
- Maintain system cards documenting training data provenance for AI products

---

## 8. IP Assignment: PIAA/CIIAA Agreements

The Proprietary Information and Inventions Assignment (PIIA/CIIAA) agreement is the **most critical IP document for any startup**. Ensures all IP created by founders, employees, and contractors is owned by the company.

### Key Provisions
- **Assignment clause**: All inventions created within scope of employment or using company resources are assigned to the company
- **Prior inventions schedule**: Employees list pre-existing IP they retain; anything not listed is assumed assigned — verify completeness
- **Confidentiality obligations**: Survive employment termination (typically indefinite)
- **Non-solicitation**: Typically 12–24 months post-employment
- **Moonlighting restrictions**: Disclosure requirements for outside activities

### Critical Failure Modes (Both Are VC/M&A Deal-Killers)
1. **Founders who built pre-incorporation code without signing PIIAs**: Code legally belongs to the individual; requires retroactive assignment agreement
2. **Contractors without explicit IP assignment clauses**: Independent contractors own their work product by default under U.S. copyright law unless there's a written assignment

**Action**: Execute PIIAs at company formation for all founders. Execute at hire for all employees. Include IP assignment language in EVERY contractor services agreement — the default is that they own it.

---

## 9. Freedom-to-Operate (FTO) Analysis

An FTO analysis determines whether your product can be commercialized without infringing third-party patents.

**Three-step process**:
1. **Claim mapping**: Identify relevant patent claims in technology space; map against your product's technical implementation
2. **Validity assessment**: Evaluate whether problematic claims are likely valid in light of prior art
3. **Design-around options**: Identify technical modifications that avoid infringement

**When to conduct**: Before product launch, before significant R&D investment, before fundraising (Series A+), before M&A.

**Cost**: Basic FTO: $5,000–$20,000. Comprehensive in densely patented spaces: $50,000+. For startups, prioritize FTO in core technology areas.

**Working around prior art**: Licensing (cross-licenses, FRAND for standards-essential patents), design-arounds, invalidity challenges via USPTO **inter partes review (IPR)**. IPR costs $30,000–$100,000 (far less than district court litigation) with 60–80% petitioner success rates for instituted cases.

---

## 10. IP Due Diligence in Fundraising and M&A

VCs and acquirers conduct structured IP diligence beginning at Series A:

**Ownership chain**: All PIIAs signed? All contractor assignments executed? Any pre-incorporation IP without proper assignment?

**Patent portfolio quality**: Granted claims mapping to shipped features and roadmap items; coherent strategy; jurisdictional coverage in key markets. One strong, well-scoped patent outweighs ten broad but weak filings.

**Freedom-to-operate**: Absence of FTO analysis is a red flag. Identified litigation risk may require escrow, indemnification carve-outs, or price adjustments.

**Open source audit**: Disclosure of any GPL/AGPL code in the product stack; missing license notices; unlicensed third-party code.

**Trade secret hygiene**: NDA coverage with all material third parties; employee confidentiality; access control documentation.

**Data and privacy**: GDPR/CCPA compliance documentation; DPAs with vendors; lawful basis for personal data used in AI training.

---

## 11. Patent Trolls and NPE Defense

**Scale of the problem (2025)**: NPEs filed **55.4% of all U.S. patent lawsuits** (up 21.6% YoY). In tech, NPEs account for 90.3% of all patent litigation. Average NPE demand: $500K–$2M in licensing demands, designed to be cheaper to settle than fight.

In Europe: UPC saw NPE claims grow 50% in 2025; NPEs = 21.8% of UPC claimants.

### Defense Toolkit

1. **Alice/§101 challenge**: Many NPE patents were filed pre-Alice claiming abstract ideas with generic computer implementation. Early motion to dismiss based on §101 ineligibility — highest-ROI defense, often disposable at the pleading stage.

2. **Inter Partes Review (IPR)**: File petition with USPTO Patent Trial and Appeal Board (PTAB) to challenge validity on prior art grounds. 60–80% petitioner success rates for instituted cases. Cost: $30,000–$100,000.

3. **Demand letter protocol**: Never ignore. Preserve all communications. Respond through counsel. Never make admissions.

4. **Defensive aggregators**: RPX Corporation, Open Invention Network (OIN, for Linux-related patents), LOT Network — subscription-based defensive coverage.

5. **Patent defense insurance**: Increasingly cost-effective for startups.

6. **UPC protective letters**: File with UPC Central Register to put courts on notice of invalidity arguments.

---

## 12. International IP: PCT, EPO, China, India

### PCT Filing Strategy
- File within **12 months** of priority date
- Reserves rights in 157 countries; 18 months to decide national phase entries (30 months from priority date)
- International search report gives early patentability signal
- Cost: $3,000–$8,000 for filing + $5,000–$15,000 per national phase entry

### EPO (European Patent Office)
- Single European Patent application covers 38 member states
- **Unitary Patent** (effective 2023): Single-validation coverage across 17+ EU states — significant cost reduction vs. individual country validation

### China (CNIPA)
- China filed the most PCT applications globally in 2024 (70,160)
- **Utility model patents** (10-year term, less rigorous examination): Faster, cheaper protection alongside invention patents
- Enforcement has improved significantly since 2019 IP court reforms
- Essential for any startup manufacturing or selling in Asia

### India
- Patent filings grew 16.5% in 2024
- Excludes "mathematical methods" and "computer programs per se" — more restrictive than U.S., but allows software claims when tied to technical effect
- Critical for tech startups with Indian engineering offices or outsourcing partners

**Timing strategy**: U.S. provisional → PCT at 12 months → national phase at 30 months (prioritize by market, manufacturing, and competitor presence)

---

## 13. Data Rights: Ownership, Licensing, GDPR

**User data ownership**: No comprehensive federal "data ownership" law in the U.S. Terms of Service govern. Users retain privacy rights under CCPA/CPRA, state laws, and GDPR for EU persons.

**Data licensing agreements** should specify: permitted uses, sublicensing rights, derivative works rights, exclusivity, audit rights, data return/destruction provisions.

**GDPR Implications for AI Startups (2024-2026)**:
- Using personal data to train AI models requires a lawful basis under GDPR Art. 6
- France's CNIL (2025): "Legitimate interest" can serve as basis if processing is necessary, proportionate, and doesn't override individual rights — not settled across all EU member states
- EDPB (March 2025): Comprehensive DPIAs required for most commercial AI systems
- Right to erasure (Art. 17): If personal data is embedded in model weights, erasure becomes computationally impractical — regulatory resolution still evolving

**Action**: Maintain data provenance records. Conduct DPIAs before AI model deployment in EU. Review all third-party data licenses for AI training scope. Ensure ToS gives sufficient rights to use user-generated content for model training.

---

## 14. 2024-2026 AI IP Legal Developments

| Date | Development | Significance |
|---|---|---|
| July 2024 | USPTO 2024 AI Patent Eligibility Guidance | Clarified Alice/Mayo for AI; provided eligible examples |
| July 2024 | EU AI Act enters into force | Adds compliance layer on top of GDPR |
| August 2024 | *Doe v. GitHub* DMCA §1202(b) dismissed | Limits DMCA claims for non-identical AI outputs |
| January 2025 | USCO Part 2 (Copyrightability) | No copyright for purely AI-generated works |
| April 2025 | *Recentive v. Fox Corp.* (Fed. Cir.) | "Do it with AI" is not patentable without technical ML innovation |
| March 2025 | EDPB GDPR & AI Models Opinion | DPIAs required; guidance on lawful basis for training |
| June 2025 | CNIL Clarifies GDPR Legitimate Interest for AI | EU basis framework for AI training on personal data |
| December 2025 | SCOTUS denies cert in *Recentive* | Cements strict ML eligibility standard |
| March 2026 | SCOTUS denies cert in *Thaler v. Vidal* | Confirms AI cannot be a named inventor in U.S. |
| 2026 (expected) | USCO Part 3 (Training Data & Licensing) | Will address fair use and licensing for AI training |
| 2026 (pending) | 9th Circuit ruling in *GitHub Copilot* | Will define DMCA §1202(b) scope for derivative AI outputs |

---

## Strategic Priority Checklist

**At formation**:
- [ ] Execute PIIAs for all founders; assign pre-incorporation IP
- [ ] File provisional patent applications on core technology
- [ ] Register primary trademark (intent-to-use)
- [ ] Implement trade secret hygiene (NDAs, access controls, classification policy)

**Pre-seed / Seed**:
- [ ] SCA scan for open source license conflicts
- [ ] Copyright register key software works
- [ ] Document training data provenance for AI products

**Series A**:
- [ ] Commission FTO analysis for core product
- [ ] File PCT application on key innovations (if 12-month window allows)
- [ ] Conduct full IP audit (ownership chain, gaps, third-party risks)
- [ ] Consider joining LOT Network or RPX for NPE defense

**Ongoing**:
- [ ] Monitor competitor patent filings (Google Patents alerts)
- [ ] File continuation patents as product evolves
- [ ] Conduct annual IP audits ahead of fundraising
- [ ] Maintain GDPR/CCPA compliance documentation
