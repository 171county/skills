---
name: business-tech-law-expert
description: Expert-level advisor on business law for technology companies. Use this skill whenever the user asks about company formation (Delaware C-Corp, LLC vs C-Corp), founder agreements (vesting, co-founder disputes), employment law (non-competes, IP assignment, contractor vs employee misclassification), privacy law (GDPR, CCPA/CPRA, state privacy laws, DPAs), AI regulation (EU AI Act, FTC AI enforcement, algorithmic accountability), SaaS contracts (MSA, ToS, SLA, DPA, AUP), cybersecurity law (SEC disclosure rules, CISA CIRCIA, incident reporting), antitrust and competition law, export controls on AI chips, TAKE IT DOWN Act, data localization requirements, or any landmark tech law cases from 2024-2026.
---

# Business Tech Law Expert

You are an expert-level legal advisor for technology companies. Give precise, actionable guidance grounded in current statutes, regulations, and case law. All information current as of June 2026.

**Important**: This skill provides legal information, not legal advice. Complex matters require qualified legal counsel.

---

## 1. Company Formation: Legal Structure Fundamentals

### Delaware C-Corporation (Default for VC-Backed Startups)
Delaware C-Corp is the near-universal structure for U.S. venture-backed companies. Key reasons:
- Well-established case law under the Delaware General Corporation Law (DGCL)
- Court of Chancery: specialized business court with predictable outcomes
- Flexible stock authorization enables preferred share structures required by VCs
- Required for QSBS eligibility (26 U.S.C. § 1202) — see Startup Finance skill
- S-Corp and LLC structures are incompatible with VC investment mechanics

**Formation checklist**:
- Certificate of Incorporation (DE Secretary of State)
- Bylaws establishing governance procedures
- Initial Board consent resolving organizational matters
- Restricted Stock Purchase Agreements (RSPAs) for founders with vesting
- 83(b) election filed within 30 days of stock issuance — **missing this deadline is irreversible and costs founders significant tax**
- Proprietary Information and Inventions Assignment (PIIA) for all founders

### LLC vs. C-Corp Decision Matrix

| Factor | C-Corp | LLC |
|---|---|---|
| VC investment | Required | Incompatible |
| QSBS eligibility | Yes (§1202) | No |
| Pass-through taxation | No (C-corp tax) | Yes |
| Self-employment tax | No (salary only) | Yes (members) |
| Acquisition targets | Preferred by buyers | Stock deal requires conversion |
| Options/equity comp | Standard (ISO/NSO) | Complex (profits interests) |

**Use LLC for**: bootstrapped businesses, real estate, professional services, single-owner consulting — not venture-backed tech.

### Foreign Qualification
Any company "doing business" in a state other than Delaware must foreign qualify in that state. Triggers: employees, office space, contracts, physical presence. Failure to qualify exposes directors to personal liability and blocks court access.

---

## 2. Founder and Employment Law

### Founder Agreements: Non-Negotiables
**Vesting schedules**: Standard 4-year, 1-year cliff. Cliff prevents departure before meaningful contribution; 4-year total aligns with typical Series A fundraise cycle. Accelerate on double-trigger (change of control + termination) — single-trigger acceleration is rare and creates M&A friction.

**Co-Founder Dispute Prevention**:
- Specify role, title, and responsibilities explicitly in a Founders Agreement
- Allocate shares before company has value — post-traction negotiations fail
- Define decision-making authority and deadlock resolution
- Include buy-sell provisions for departing founders

### Non-Compete Law: Post-FTC Rule Vacatur

**Ryan LLC v. FTC** (N.D. Texas, Aug. 20, 2024): Federal court vacated the FTC's nationwide non-compete rule before it took effect. The rule would have banned virtually all non-competes for workers. The 5th Circuit affirmed the vacatur in December 2024. The Supreme Court denied cert in April 2026.

**Current state (2026)**:
- Non-competes remain governed by **state law**
- California, North Dakota, Minnesota, Oklahoma: non-competes are unenforceable (with narrow exceptions)
- Most other states: enforce "reasonable" non-competes (limited scope, geography, and duration)
- The FTC may attempt a narrower rule targeting non-competes above certain compensation thresholds — watch for 2026-2027 rulemaking

**Non-solicitation clauses**: Remain enforceable in most states regardless of non-compete status. More defensible than non-competes; use these for protecting customer relationships and team poaching.

### Contractor vs. Employee Misclassification
The DOL's **Worker Classification Rule** (effective March 2024) restored the multi-factor "economic reality" test under the FLSA — harder to classify workers as independent contractors.

**ABC Test** (California's AB5, adopted by 12+ states):
- A: Worker is free from control and direction
- B: Work is outside the usual course of business
- C: Worker is customarily engaged in an independently established trade

**Misclassification costs**: Back wages, unpaid benefits, employer payroll taxes, penalties. IRS Form SS-8 triggers audit. Class action exposure under California PAGA.

**Safe practices**: Written contracts specifying independent contractor status, project-based (not time-based) compensation, worker provides own tools, no exclusivity, no performance reviews.

---

## 3. Privacy Law Landscape (2026)

### State Privacy Law Explosion
As of January 1, 2026, **19 states** have comprehensive privacy laws in effect:

| State | Law | Enforcement |
|---|---|---|
| California | CCPA/CPRA (2023 full enforcement) | CA Privacy Protection Agency (CPPA) |
| Colorado | CPA | AG |
| Connecticut | CTDPA | AG |
| Virginia | VCDPA | AG |
| Texas | TDPSA | AG |
| Florida | FDBR | AG (>$1B revenue only) |
| Iowa | ICDPA | AG |
| Indiana | ICDPA | AG |
| Montana | MCDPA | AG |
| Tennessee | TIPA | AG |
| Oregon | OCPA | AG |
| Delaware | DPDPA | AG |
| New Hampshire | NH Privacy Act | AG |
| New Jersey | NJDPA | AG |
| Kentucky | KCDPA | AG |
| Nebraska | NDPA | AG |
| Maryland | MODPA | AG (effective Oct 2025) |
| Minnesota | MHMD | AG (effective July 2025) |
| Rhode Island | RICDPA | AG (effective Jan 2026) |

**Common obligations across frameworks**: Right to access, correct, delete, and opt out of sale/targeted advertising; data minimization; purpose limitation; vendor agreements (DPAs); privacy notices.

**California CPRA enforcement acceleration (2026)**: CPPA issued its first enforcement actions in 2025 targeting dark patterns in consent interfaces and inadequate data broker registrations. Fines: $2,500–$7,500 per intentional violation.

### GDPR Fundamentals for U.S. Tech Companies
GDPR applies to any company processing EU/EEA resident personal data — regardless of company location.

**Six lawful bases (Art. 6)**: Consent, Contract, Legal Obligation, Vital Interests, Public Task, Legitimate Interest.

**For AI companies specifically** (see IP Law skill §13): Using personal data to train AI models requires a documented lawful basis. EDPB March 2025 opinion: DPIAs required for most commercial AI systems. CNIL clarified legitimate interest can apply if proportionate — not uniform across EU member states.

**GDPR enforcement escalation (2025-2026)**:
- Meta: €1.2B fine (2023), €251M (Jan 2025) for Facebook data breach
- X/Twitter: €550M for unauthorized data transfers
- U.S. companies face heightened enforcement since invalidation of Privacy Shield (Schrems II)
- Data Transfer Mechanisms: Standard Contractual Clauses (SCCs, 2021 version) or EU-US Data Privacy Framework (valid as of 2023 adequacy decision)

---

## 4. AI Regulation: The 2026 Compliance Landscape

### EU AI Act (Regulation 2024/1689)
**The most consequential AI regulation globally.**

**Timeline**:
- August 2, 2024: Entered into force
- February 2, 2025: Bans on prohibited AI practices active (social scoring, real-time biometric surveillance, subliminal manipulation)
- August 2, 2025: GPAI (General-Purpose AI) model obligations active
- **August 2, 2026**: High-risk AI system obligations fully active
- August 2, 2027: Obligations for existing high-risk systems in regulated products

**Risk tiers**:
- **Prohibited**: Unacceptable risk (social scoring, cognitive behavioral manipulation)
- **High-risk**: Subject to conformity assessment, mandatory HRAIA (Human Rights Impact Assessment), Article 73's **15-day serious incident reporting requirement**, CE marking for deployment in EU
- **Limited risk**: Transparency obligations (chatbots must disclose AI nature)
- **Minimal risk**: No obligations

**High-risk categories include**: Biometric identification, critical infrastructure, education and vocational training, employment (CV screening, performance evaluation), essential services, law enforcement, border management, administration of justice.

**GPAI obligations (active Aug 2025)**:
- Technical documentation and transparency disclosures
- Copyright compliance policies for training data
- Systemic risk models (≥10²⁵ FLOPs): mandatory red-teaming, incident reporting, adversarial testing

**Key compliance steps**: Register in EU AI Act database, conduct conformity assessment, appoint EU representative if not EU-based, maintain technical documentation.

### FTC AI Enforcement (U.S.)
FTC Chairwoman Lina Khan was replaced by Andrew Ferguson in January 2025 — enforcement philosophy shifted from structural to conduct remedies, but AI enforcement continues.

**FTC Section 5** (unfair or deceptive acts): Applies to AI claims. FTC issued warning letters in 2025 to companies making unsubstantiated claims about AI capabilities.

**AI Guidance documents**: FTC "Loot Boxes" (2024), AI in hiring guidance (2024), AI claims enforcement policy (2025). Core principle: AI products are subject to the same consumer protection laws as any product — accuracy claims must be substantiated.

**Algorithmic Discrimination**: Equal Credit Opportunity Act (ECOA), Fair Housing Act, and Title VII all apply to AI-based decisions in credit, housing, and employment. CFPB (2024) guidance: lenders must explain adverse AI decisions to consumers.

---

## 5. SaaS Contract Stack

Every B2B SaaS company needs this core contract suite:

### Master Service Agreement (MSA)
The governing legal framework for the customer relationship. Key provisions:
- **Limitation of liability**: Cap at 12 months of fees paid; mutual exclusion of consequential damages
- **Indemnification**: Customer indemnifies for their data; vendor indemnifies for IP infringement
- **Termination**: For cause (material breach, 30-day cure period); for convenience with notice
- **Governing law**: Delaware or New York; avoid customer's home state
- **SLA**: Define uptime commitments and service credit remedies (not cash refunds — credit only)

### Data Processing Agreement (DPA)
Mandatory for any processing of EU personal data under GDPR Art. 28. Required by CCPA for service providers. Key elements:
- Subject matter, nature, purpose, and duration of processing
- Type of personal data and categories of data subjects
- Obligations and rights of the controller
- Subprocessor restrictions and approval mechanism
- Data return or deletion obligations upon termination
- Audit rights

**Practical note**: Enterprises will not sign contracts without a compliant DPA. Have one pre-drafted and attached to your standard MSA.

### Terms of Service (ToS)
For self-serve/PLG products. Binding on customers through clickwrap acceptance. Must include:
- Acceptable use policy (AUP) by reference or embedded
- Suspension and termination rights for policy violations
- License grant (what customers can do with the software)
- Customer data ownership and your rights to process it
- Feedback license (you can use feature requests and feedback)
- AI training rights (if you use customer data to train models — explicitly include or exclude)

### Service Level Agreement (SLA)
- Define "uptime" precisely (exclude scheduled maintenance, force majeure, customer-caused outages)
- Measurement period: calendar month
- Standard commercial SaaS: 99.9% uptime (8.7 hours downtime/year)
- Enterprise: 99.95% (4.4 hours/year); consider 99.99% (52 minutes/year) for critical systems
- Remedies: tiered service credits (10-30% of monthly fees) for SLA breach; never cash refunds

---

## 6. Cybersecurity Law: Mandatory Reporting

### SEC Cybersecurity Disclosure Rules (Effective December 2023)
**Public companies (and future IPO candidates)** must:
- Report **material** cybersecurity incidents on Form 8-K within **4 business days** of determining materiality
- Annual disclosure of cybersecurity risk management program in Form 10-K
- Board-level cybersecurity expertise disclosure

**Materiality determination**: Not every breach is "material." Legal counsel and management determine based on business impact, data sensitivity, and likely investor significance. The 4-day clock starts at materiality determination, not incident discovery.

**Ongoing obligation**: Update 8-K disclosure if material information changes during investigation.

### CISA CIRCIA (Cyber Incident Reporting for Critical Infrastructure Act)
Applies to critical infrastructure sectors (finance, healthcare, energy, transport, telecom). Proposed rules published by CISA in 2024:
- **72 hours** to report significant cyber incidents
- **24 hours** to report ransomware payments
- Final rules expected 2025-2026

### State Data Breach Notification Laws
All 50 states + D.C. have breach notification laws. Timing varies: California requires notification "in the most expedient time possible," generally interpreted as 30–45 days. HIPAA: 60 days for breaches affecting ≥500 individuals. Notify state AGs + affected individuals simultaneously.

---

## 7. Antitrust and Competition Law

### U.S. v. Google (Search Monopoly)
**Judge Mehta ruled August 5, 2024**: Google illegally maintained its search monopoly through exclusive distribution agreements with Apple, Samsung, and device manufacturers. Constituted monopolization under Sherman Act §2.

**Remedies phase (ongoing)**: DOJ proposed divestiture of Chrome, Android restrictions, and prohibition on exclusive distribution contracts. Final remedy order expected 2025-2026. **Biggest tech antitrust ruling since U.S. v. Microsoft (2001).**

### FTC v. Meta
FTC's second attempt to force divestiture of Instagram and WhatsApp dismissed by Judge Boasberg (June 2025). Found FTC failed to define the relevant market properly and could not show Meta's acquisitions harmed competition in "personal social networking."

### EU Digital Markets Act (DMA) Enforcement
In force since September 2023. Designates "gatekeepers" (Apple, Google, Meta, Amazon, Microsoft, TikTok/ByteDance). Requires interoperability, third-party access, anti-self-preferencing. Fines up to 10% of global revenue; 20% for repeat offenders.

**Apple non-compliance findings (2024-2025)**: Apple's App Store compliance plan for DMA found non-compliant twice. Fine exposure: billions of euros.

---

## 8. Export Controls: AI Chips and Technology

### EAR (Export Administration Regulations) and AI Chips
BIS (Bureau of Industry and Security) issued **AI Chip Export Controls**:

**October 2023 (updated October 2024)**: Advanced semiconductor export controls restricting NVIDIA A100, H100, H200, and equivalent chips to China, Russia, and other listed countries without license.

**January 2025 "AI Diffusion Rule"**: Created a tiered framework:
- **Tier 1** (close allies): Unlimited access
- **Tier 2** (most countries): Annual computational limits without license
- **Tier 3** (adversary nations: China, Russia, Iran, North Korea): Strict license requirements or prohibition

**Trump administration revision (May 2025)**: Replaced the Biden-era AI Diffusion Rule with a new framework, withdrawing the Tier 2 country-level caps and focusing controls more narrowly on adversary nations. Broader market access for U.S. cloud compute for trusted partners.

**Compliance obligation for SaaS/AI companies**: If your cloud infrastructure provides GPU access or runs AI models accessible to restricted parties, EAR compliance applies. KYC processes for compute customers required.

### OFAC Sanctions
Separate from EAR. Prohibit any transaction with designated Specially Designated Nationals (SDNs). AI companies providing services to SDN-listed individuals or entities (including through resellers) violate OFAC. Screen at onboarding.

---

## 9. Platform Liability and Content Law

### Section 230 (47 U.S.C. § 230)
Provides immunity for platforms hosting third-party content. Does NOT protect:
- Platform's own content
- Federal criminal liability
- FOSTA-SESTA (sex trafficking content)

**Current status (2026)**: Multiple Section 230 reform bills introduced annually in Congress but none passed. *Gonzalez v. Google* (SCOTUS 2023) declined to narrow Section 230 algorithmically. The law remains intact but faces growing political pressure.

### TAKE IT DOWN Act (Signed May 19, 2025)
Criminalizes non-consensual intimate imagery (NCII), including AI-generated deepfakes. Key provisions:
- Platforms must remove reported NCII within **48 hours** of notification
- Failure to comply: up to $50,000 civil penalties per violation
- Applies to **generative AI tools** that create such content
- FTC enforcement authority

**Compliance implication for AI companies**: Image/video generation tools must implement NCII detection and takedown workflows. Failure to do so creates FTC enforcement and civil liability risk.

---

## 10. Data Localization and Cross-Border Transfers

### Jurisdiction-by-Jurisdiction Requirements

| Jurisdiction | Requirement |
|---|---|
| **EU/EEA (GDPR)** | Transfer mechanism required (SCCs, adequacy decision, BCRs); no transfers to countries without adequate protection without mechanism |
| **China (PIPL)** | Cross-border data transfer requires PIPL-compliant contract or CAOC security assessment for sensitive/large-scale data |
| **Russia (Federal Law No. 242-FZ)** | Russian personal data must be stored on servers within Russia before transfer |
| **India (DPDPA 2023)** | Transfers restricted to countries notified as permitted by Indian government; rules still being finalized |
| **Brazil (LGPD)** | Transfers permitted to countries with adequate protection or under equivalent safeguards |
| **U.S.** | No federal data localization law; some sector-specific requirements (ITAR, HIPAA) |

### EU-U.S. Data Privacy Framework (2023)
Effective July 2023 adequacy decision. U.S. companies self-certify with DOC. Provides a valid transfer mechanism for EU → U.S. transfers. Subject to CJEU review — potential Schrems III challenge in 2026-2027.

---

## 11. 2024-2026 Landmark Tech Law Timeline

| Date | Development | Impact |
|---|---|---|
| March 2024 | DOL Worker Classification Rule effective | Tighter independent contractor test |
| August 2024 | Google Search Monopoly ruling (U.S. v. Google) | First major antitrust remedy in 20+ years |
| August 2024 | FTC Non-Compete Rule vacated (Ryan LLC v. FTC) | Non-competes remain state law issue |
| August 2024 | EU AI Act fully in force | Global AI compliance framework launched |
| October 2024 | NVIDIA AI chip controls updated | New tiered export control regime |
| February 2025 | EU AI Act prohibited practices ban active | No social scoring, biometric surveillance in EU |
| May 2025 | TAKE IT DOWN Act signed | AI deepfake NCII: 48-hour platform takedown |
| May 2025 | AI Diffusion Rule revised by Trump admin | Narrower chip controls vs. Biden framework |
| June 2025 | FTC v. Meta dismissed | Definition of social networking market rejected |
| August 2025 | EU AI Act GPAI obligations active | Model providers: transparency + copyright compliance |
| December 2025 | AICOA (AI Competition and Oversight Act) introduced | Potential U.S. federal AI oversight framework |
| **August 2026** | **EU AI Act High-Risk AI fully active** | **Conformity assessment, CE marking, 15-day incident reporting** |

---

## Legal Compliance Checklist by Stage

**At Formation**:
- [ ] Delaware C-Corp incorporation
- [ ] Founder RSPAs with 4-year vesting, 1-year cliff
- [ ] 83(b) elections filed within 30 days
- [ ] PIIAs for all founders
- [ ] Basic IP assignment from pre-incorporation work
- [ ] Privacy policy and ToS for any user-facing product

**Pre-Seed / Seed**:
- [ ] Employee PIIAs and confidentiality agreements at hire
- [ ] Contractor agreements with explicit IP assignment
- [ ] GDPR-compliant privacy policy + DPA template
- [ ] Data processing map (what data, where stored, who processes)
- [ ] Acceptable use policy for any AI product

**Series A**:
- [ ] Standard MSA + DPA suite for B2B sales
- [ ] SLA with defined uptime and service credit terms
- [ ] Cybersecurity incident response plan + legal hold procedures
- [ ] Export control review for any restricted country sales
- [ ] Employment counsel review of non-solicitation agreements by state
- [ ] EU AI Act preliminary risk classification

**Growth / Pre-IPO**:
- [ ] SEC cybersecurity materiality determination process
- [ ] State privacy law compliance program (all 19 states)
- [ ] CISA/CIRCIA critical infrastructure sector assessment
- [ ] EU AI Act conformity assessment for high-risk systems
- [ ] TAKE IT DOWN Act compliance if platform or AI image/video product
- [ ] Antitrust counsel on any M&A or distribution agreements
