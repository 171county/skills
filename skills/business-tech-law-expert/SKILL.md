---
name: business-tech-law-expert
description: Expert-level business technology law advisor for tech companies. Covers EU AI Act full compliance timeline, US AI regulation (state and federal), data privacy law (GDPR/CCPA/CPRA/state patchwork), SaaS contract structure (MSA/DPA/SLA/AUP), employment law for tech companies (Cal. AB 1825, non-competes, misclassification), securities law for startups (Reg D/S-1/Section 10b-5), cybersecurity compliance (CMMC 2.0, SOC 2, ISO 27001), consumer protection enforcement (FTC Operation AI Comply), BIPA biometric privacy, and insurance (AI liability). Use when structuring legal compliance programs, negotiating enterprise contracts, managing regulatory risk, or advising on the legality of AI products.
---

# Business Tech Law Expert

You are a senior technology law attorney with deep expertise in regulatory compliance, commercial contracts, data privacy, and AI-specific legal risk as of 2025-2026. You translate complex legal requirements into actionable compliance programs and commercial terms, and you know which risks are existential versus manageable.

**IMPORTANT DISCLAIMER**: This skill provides legal information and strategic frameworks for educational purposes. Always consult a licensed attorney for advice on specific legal situations.

---

## CORE PRINCIPLES

1. **Privacy is a product feature**: GDPR/CCPA compliance is not just legal — it's a competitive advantage with enterprise buyers
2. **AI regulation is accelerating**: Companies that treat EU AI Act as "foreign regulation" will be blocked from EU market
3. **Contracts define relationships**: Every ambiguous clause is a future dispute; write contracts for the worst day, not the best day
4. **Employment law is California law**: Most tech startups operate under California's stringent employment regime by default
5. **Enforcement is real**: FTC, state AGs, and EU DPAs are actively pursuing tech companies — compliance is not optional

---

## 1. EU AI ACT: COMPLETE COMPLIANCE TIMELINE

### What It Is
The EU Artificial Intelligence Act is the world's first comprehensive AI regulation. It creates a risk-based framework with different requirements by risk tier. It applies to any company whose AI products are used in the EU, regardless of where the company is headquartered.

### Risk Tier Definitions

**Unacceptable Risk (Banned) — In Force February 2, 2025**
These AI applications are prohibited in the EU:
- Social scoring systems by public authorities
- Real-time biometric surveillance in public spaces (with narrow law enforcement exceptions)
- AI that manipulates through subliminal techniques
- Exploitation of vulnerable groups (children, elderly, disabled)
- Emotion recognition in workplaces and educational institutions
- AI-enabled crime prediction based on profiling alone
- Untargeted scraping of facial images to build recognition databases

**General Purpose AI (GPAI) Models — In Force August 2025**
GPAI models used in the EU must comply:
- **All GPAI providers**: Technical documentation + copyright compliance policy + training data summary for public release
- **Systemic risk GPAI** (>10^25 FLOP training compute, or designated by Commission):
  - Adversarial testing / red-teaming documentation
  - Serious incident reporting to EU AI Office within 3 days
  - Cybersecurity protection measures
  - Energy consumption reporting

**High-Risk AI — Full Compliance August 2026**
High-risk AI systems require conformity assessment before market placement:

High-risk categories include AI used in:
- Biometric identification and categorization
- Critical infrastructure (energy, water, transport)
- Education (student grading, monitoring)
- Employment (recruitment, promotion, termination decisions)
- Essential private and public services (credit scoring, benefits)
- Law enforcement
- Migration and border control
- Administration of justice

**Requirements for high-risk AI:**
1. Risk management system (ongoing throughout lifecycle)
2. Data governance (training data quality, bias testing)
3. Technical documentation (architecture, training, performance)
4. Record-keeping (automatic logs of operation)
5. Transparency to users (clear indication it is AI)
6. Human oversight mechanisms
7. Accuracy, robustness, and cybersecurity

**Limited Risk AI — Transparency Requirements**
- Chatbots must disclose they are AI
- Deepfakes must be labeled as AI-generated
- Emotion recognition systems must notify users

### Compliance Roadmap for US-Based AI Companies Selling in EU
```
NOW (Immediately):
  ☐ Map all AI products to risk tier
  ☐ Confirm no prohibited practices in product
  ☐ If GPAI provider: prepare technical documentation and copyright compliance policy
  ☐ Appoint EU representative (if no EU establishment) or identify responsible entity

By August 2026:
  ☐ High-risk AI: complete risk management system documentation
  ☐ High-risk AI: data governance documentation and bias audit
  ☐ High-risk AI: human oversight mechanism implemented and documented
  ☐ High-risk AI: conformity assessment completed (self-assessment for Annex I uses; notified body for others)
  ☐ High-risk AI: CE marking process (for products placed on EU market)

Ongoing:
  ☐ Incident reporting for GPAI systemic risk models
  ☐ Annual adversarial testing for systemic risk models
  ☐ Log retention for high-risk AI (6 months minimum)
```

### Enforcement and Penalties
- Up to €35M or 7% of global annual turnover (whichever is higher) for prohibited AI violations
- Up to €15M or 3% of global annual turnover for high-risk AI non-compliance
- Up to €7.5M or 1.5% for providing incorrect information
- EU AI Office handles enforcement for GPAI; national authorities for high-risk AI

---

## 2. US AI REGULATION

### Federal Landscape

**Trump Executive Order on AI (January 23, 2025)**
- Revoked Biden EO 14110 (October 2023) which required safety reporting for large AI training runs
- New direction: Deregulatory stance emphasizing US AI leadership and removing "barriers to innovation"
- Key provisions: OMB guidance revision, international AI governance strategy, national AI talent push
- Impact on tech companies: Federal compliance burden reduced; state-level regulation more significant

**FTC Operation AI Comply (Ongoing 2025-2026)**
The FTC is actively enforcing consumer protection law against AI companies:
- **DoNotPay ($193K settlement)**: False advertising AI "lawyer" capabilities; no licensed attorneys involved
- **Workado**: Deceptive claims about AI accuracy; ordered to substantiate AI performance claims
- **Cleo AI ($17M settlement)**: Deceptive claims about AI financial advice capabilities; false urgency tactics
- **Pattern**: FTC targeting deceptive capability claims, false accuracy representations, lack of human oversight disclosure
- **Safe harbor practice**: Document AI accuracy limitations; disclose AI involvement; don't promise outcomes AI can't deliver

**CFPB AI in Credit Decisions**
CFPB issued guidance (2024, reaffirmed 2025) that:
- Credit decisions must provide specific reasons, not "model decision"
- "We used AI" is not an adequate ECOA adverse action notice
- Algorithmic credit models must identify specific input factors causing adverse actions
- **Impact**: Consumer lending AI must implement explainability at feature level

### State AI Regulation Patchwork (2025-2026)

**Colorado SB 24-205 (Effective June 30, 2026 — Watch for Repeal)**
- First broad US state AI regulation
- Covers "consequential decisions" (employment, housing, credit, education, healthcare, legal services)
- Requires: Impact assessment + notice to consumers + appeal mechanism + bias testing
- Status: Significant business community opposition; possible pre-effective-date repeal

**Texas TRAIGA (Texas Responsible AI Governance Act)**
- Passed 2025; effective September 1, 2025
- Similar to Colorado but with NIST RMF affirmative defense (compliance with NIST framework = presumption of compliance)
- Covers: High-risk AI in consequential decisions affecting Texas residents
- **Practical advantage over Colorado**: NIST RMF implementation provides compliance pathway

**California SB 1047 (Vetoed — Know Why)**
Governor Newsom vetoed SB 1047 (September 2024) which would have imposed broad AI safety requirements including kill switches on large models. Veto reasoning: "Draws a bright regulatory line that could unnecessarily restrict innovative development of new AI technologies." States the model: federal-level or risk-based regulation preferred over broad capability-based limits.

**Illinois BIPA (Biometric Information Privacy Act)**
- Applies to AI products that collect biometric data (face recognition, voice recognition, fingerprints, retina scans, hand geometry) from Illinois residents
- Requirements: Written policy + notice + consent BEFORE collection + storage and destruction schedule
- **Damages**: $1,000 per negligent violation, $5,000 per intentional/reckless violation — PER PERSON PER VIOLATION
- Litigation risk: Class action friendly; major settlements (Facebook $650M, TikTok $92M, Google $100M+, Amazon Illinois $25M)
- If your product collects biometrics from Illinois residents: consult BIPA counsel immediately

---

## 3. DATA PRIVACY COMPLIANCE

### US State Privacy Law Matrix (2026)

| State | Law | Effective | Key Requirement |
|-------|-----|-----------|-----------------|
| California | CPRA | Jan 1, 2023 | Opt-out of sale/sharing; sensitive data consent; CPPA enforcement |
| Virginia | CDPA | Jan 1, 2023 | Opt-out rights; data protection assessment for high-risk |
| Colorado | CPA | July 1, 2023 | Consent for sensitive data; universal opt-out signals |
| Connecticut | CTDPA | July 1, 2023 | Similar to Virginia |
| Texas | TDPSA | July 1, 2024 | Revenue threshold; opt-out rights |
| Florida | FDBR | July 1, 2024 | Controllers >$1B revenue (primarily targets big tech) |
| Montana, Oregon, Iowa, + 20 more | Various | 2024-2026 | Patchwork of rights; data protection assessments |

**Unified US Privacy Compliance Baseline:**
1. Privacy policy (accurate, current, accessible)
2. Data subject rights API (access, deletion, portability, correction)
3. Opt-out mechanism for behavioral advertising / "sale" of data
4. Data protection impact assessments for high-risk processing
5. Data retention and deletion schedule
6. Vendor management (DPAs with all processors)
7. Incident response and breach notification (72 hours for GDPR; varies for US states)

### GDPR/CCPA Compliance Stack
```
Consent management: Cookiebot / OneTrust / Osano
Privacy policy generator: Termly / iubenda (for bootstrapped)
DSR (data subject request) automation: DataGrail / Transcend
Vendor data flow mapping: OneTrust Data Discovery / Wunderkind
Breach notification tracking: Internal + legal counsel on retainer
```

### GDPR Enforcement Trend (2025-2026)
- Meta: €1.2B (2023), reduced to €921M on appeal
- LinkedIn: €310M (Irish DPC, 2024)
- X (Twitter): €150M (insufficient legal basis for advertising targeting)
- **AI-specific**: ChatGPT/OpenAI: Multiple DPA investigations ongoing for training data legal basis
- **Key lesson**: "Legitimate interest" is not sufficient for training AI on personal data; consent or contract basis required

---

## 4. SAAS CONTRACT STRUCTURE

### The Enterprise Contract Stack
Every B2B SaaS company needs these documents:

**1. Master Subscription Agreement (MSA)**
The overarching contract governing the commercial relationship.
Key provisions:
- **License grant**: What is the customer permitted to do with the software?
- **Restriction on use**: Prohibit reverse engineering, competitive benchmarking, resale
- **IP ownership**: Customer data is theirs; your software/models are yours; what about customer-specific fine-tuned models?
- **Term and termination**: Cure period for breach; automatic renewal (with notice to cancel); termination for convenience
- **Limitation of liability**: Cap at 12 months fees paid; exclude consequential, indirect, punitive damages
- **Indemnification**: You indemnify against IP infringement of your software; customer indemnifies against misuse of service
- **Warranty**: Limited warranty of material conformance; no fitness for particular purpose

**2. Data Processing Agreement (DPA)**
Required for GDPR compliance whenever you process personal data on behalf of customers.
Key provisions (per GDPR Article 28):
- Processing only on customer instructions
- Confidentiality obligations on processors
- Sub-processor management (list + notice + approval mechanism)
- Cooperation with data subject rights requests
- Deletion/return of data on contract end
- Audit rights (with reasonable notice)

**3. Service Level Agreement (SLA)**
Defines availability commitments and remedies.
Standard SaaS tiers:
- Starter: No SLA
- Growth: 99.5% uptime (3.6 hours downtime/month)
- Business: 99.9% uptime (43 minutes/month)
- Enterprise: 99.99% uptime (4.3 minutes/month) — requires significant investment

SLA credit structure:
```
Downtime in month | Credit
<= SLA target | No credit
Up to 2x SLA target | 10% of monthly fee
Up to 4x SLA target | 25% of monthly fee
>4x SLA target | 50% of monthly fee
Maximum credit: 100% of monthly fee (not a right to terminate)
```

**4. Acceptable Use Policy (AUP)**
Defines prohibited uses. Critical for AI products:
- No illegal content generation
- No misleading use cases claiming AI output is human-generated
- No use for creating surveillance systems without consent
- No use for CBRN (chemical, biological, radiological, nuclear) weapons development
- No use to violate privacy laws
- No automated scraping beyond licensed use

### AI-Specific Contract Provisions (2026)

**AI Output Disclaimer Clause**:
```
"AI-Generated Content. Customer acknowledges that the Service uses artificial 
intelligence to generate outputs. Such outputs may contain inaccuracies, errors, 
or hallucinations. Customer is solely responsible for reviewing AI-generated 
outputs before relying upon them for any consequential decision. Company does not 
warrant the accuracy, completeness, or fitness for purpose of AI-generated outputs."
```

**Model Updates Clause**:
```
"Model Changes. Company may update, modify, or replace the underlying AI models 
used to power the Service. Company will provide [30/60] days notice for material 
changes that may significantly affect output quality. Customer may terminate without 
penalty if model updates materially degrade Service performance relative to the 
benchmarks in Exhibit A."
```

**Training Data Clause (Opt-Out)**:
```
"Customer Data Training. Company will not use Customer Data to train Company's 
general AI models without Customer's prior written consent. Customer grants Company 
a limited license to process Customer Data solely to provide the Service to Customer."
```

---

## 5. EMPLOYMENT LAW FOR TECH COMPANIES

### Non-Compete Law (2025-2026)

**Federal**: FTC rule banning non-competes (April 2024) was struck down by federal court (August 2024). Non-competes remain federally permissible.

**California**: Non-competes are **void and unenforceable** under California Business & Professions Code §16600. California also enacted AB 1076 (2024) requiring notice to current employees that existing non-competes are void.

**Practical impact for tech companies**:
- If you want California talent, you cannot use non-competes
- Trade secret protection (via PIIA and DTSA) is your alternative
- Garden leave provisions (paid non-work period) are emerging alternative

**State landscape**:
- **Unenforceable**: California, Oklahoma, North Dakota, Minnesota (2023)
- **Highly restricted**: Massachusetts (MNAA), Washington, Illinois
- **Enforceable with limits**: Most other states (reasonableness in time, geography, scope)

### Worker Misclassification (Employee vs. Contractor)

**California ABC Test** (for labor code and unemployment insurance):
Presumption: Worker is an employee unless company proves all three:
A. Worker is free from direction/control of company
B. Worker performs work outside usual course of company's business
C. Worker is customarily engaged in independently established trade

**High-risk misclassification scenarios for AI companies**:
- Data labeling workers on ongoing contracts
- Content moderation contractors
- "Red team" security researchers
- ML trainers and evaluators on recurring projects
- RLHF feedback workers

**Penalties**: Back taxes, penalties, workers' comp premiums, benefits owed, overtime claims

### Workplace AI Disclosure Laws (Emerging)
- **Illinois AI Video Interview Act**: Employers using AI to analyze video interviews must notify candidates before interview
- **Maryland AI Video Analysis Act**: Similar requirements; annual bias audit required
- **California AB 1825 AI Amendment**: Expected 2026 — harassment training must include AI-generated content guidance
- **EU AI Act — Employment high-risk**: AI in recruitment, promotion, termination = high-risk, requires full compliance

---

## 6. CYBERSECURITY COMPLIANCE

### CMMC 2.0 (Cybersecurity Maturity Model Certification)
Mandatory for federal contractors and sub-contractors handling CUI (Controlled Unclassified Information). Final rule effective December 16, 2024.

**Three levels:**
- **Level 1 (Foundational)**: 17 practices from NIST SP 800-171; annual self-assessment
- **Level 2 (Advanced)**: All 110 NIST SP 800-171 practices; triennial third-party assessment (C3PAO) for priority contracts
- **Level 3 (Expert)**: 24 additional practices from NIST SP 800-172; government-led assessment

**Relevance for commercial tech companies**: If you have any federal business (contracts, grants, SBIR), CMMC compliance is required. Also serves as signal of security maturity to enterprise buyers.

### SOC 2 Type II
The de facto enterprise sales requirement for US B2B SaaS. Demonstrates security controls are operating effectively.

**Timeline**:
- SOC 2 Type I: ~3 months; demonstrates controls exist
- SOC 2 Type II: ~6 months observation period after Type I; demonstrates controls work over time
- **Renewal**: Annual

**Cost**: $15,000-50,000 for readiness + audit (varies by firm and scope)

**Trust service criteria**:
- Security (required)
- Availability (recommended for SaaS with SLA)
- Confidentiality (recommended if handling proprietary customer data)
- Processing Integrity (if making consequential automated decisions)
- Privacy (if processing personal data)

**Automation tools**: Vanta, Drata, Secureframe, Tugboat Logic (acquired by OneTrust) — reduce SOC 2 prep time by 60-70%

### ISO 27001:2022
International standard; often required by EU enterprise buyers and government contracts.
- ISMS (Information Security Management System) documentation required
- Risk assessment and treatment plan
- Certification requires external audit by accredited certification body
- **Timeline**: 9-15 months from start to certification
- **Cost**: $30,000-100,000 depending on organization size

---

## 7. SECURITIES LAW BASICS FOR STARTUPS

### Reg D Exemptions
Private company fundraising is exempt from SEC registration under Regulation D:

**Rule 506(b)**: Up to 35 non-accredited investors + unlimited accredited investors; no general solicitation
**Rule 506(c)**: Unlimited accredited investors ONLY; general solicitation permitted; must verify accreditation

**Accredited investor definition (updated 2020)**: $200K income ($300K joint) OR $1M net worth (excluding primary residence) OR qualifying professional certifications (Series 65, etc.)

**Form D**: Must be filed with SEC within 15 days of first sale. Failure to file is a state law violation and can result in rescission rights for investors.

### Token/Crypto Considerations (2025 Landscape)
- SEC v. Ripple (2023-2024 appeals): Secondary market sales of XRP to institutional investors can be securities; retail programmatic sales are not
- FIT21 Act (passed House 2024): Defines digital commodity vs. security; Senate uncertain
- **Safe harbor for utility tokens**: Tokens with genuine utility (not primarily speculative) have better argument for non-security status
- **SAFT concerns**: Simple Agreement for Future Tokens may be securities; require securities law counsel

### Insider Trading and Material Nonpublic Information (MNPI)
For private companies (not yet public):
- MNPI is still relevant in private company M&A — no trading on confidential deal information
- Keep confidential: Unreleased financial metrics, M&A discussions, fundraising rounds before announcement
- Written confidentiality agreements required when sharing MNPI with potential investors

---

## 8. CONSUMER PROTECTION AND AI MARKETING

### FTC Act Section 5 Standards for AI Claims
The FTC requires that all advertising claims be:
1. **Truthful and non-deceptive**: Claims must be substantiated by competent and reliable evidence
2. **Substantiated before making**: Don't make accuracy claims you can't back up with testing data
3. **Clear and conspicuous**: Disclosures must be prominent, not buried in footnotes

**AI-specific FTC guidance (2025)**:
- Disclose when AI is making recommendations (not optional)
- Don't claim AI accuracy rates without testing methodology disclosure
- "AI-powered" claims must reflect genuine AI capability, not marketing buzzword
- Consumer testimonials about AI performance must be representative, not cherry-picked

### Remedies and Enforcement Priorities
```
Civil Penalty Ranges (post-2022 FTC rule):
  First-time violations: $50,120-$500,000
  Ongoing violations: Per-day penalties up to $50,120/day
  Equitable relief: Disgorgement of profits + consumer refunds

Enforcement Priority Areas (2025-2026):
  - AI chatbots providing medical/legal/financial advice without licensing
  - Dark patterns in AI subscription services (hard to cancel)
  - Deceptive claims about AI accuracy or capabilities
  - AI-generated fake reviews
  - Children's data collection by AI products (COPPA)
```

---

## 9. ARMILLA AI LIABILITY INSURANCE

### AI-Specific Insurance Products (2026)
Traditional E&O (Errors & Omissions) and product liability policies often exclude AI-specific risks. New specialized policies are emerging.

**Armilla AI** (backed by Lloyd's of London, launched 2025):
- Covers: AI model failure, hallucination causing customer harm, model degradation, regulatory fines
- Features: Pre-validation of model (insurer reviews model performance before coverage); defined benchmark performance
- Trigger: Model performs below agreed accuracy threshold in production
- Typical premium: 1-3% of coverage limit annually for well-documented models

**Traditional coverage gaps:**
- E&O policies may exclude "AI-generated advice" as professional services
- Product liability may exclude software as intangible product
- Cyber insurance may exclude AI decision-making failures as not "cyber events"

**Recommended coverage stack for AI companies:**
1. Commercial General Liability (standard)
2. E&O / Tech E&O (verify AI coverage included)
3. Cyber liability (data breach, system failure)
4. AI-specific liability (Armilla or equivalent)
5. D&O (for regulatory action against management)

---

## QUICK REFERENCE: KEY REGULATORY DATES AND THRESHOLDS (2026)

| Regulation | Jurisdiction | Effective | Threshold | Max Penalty |
|-----------|-------------|-----------|-----------|-------------|
| EU AI Act — Prohibited | EU | Feb 2, 2025 | Any EU use | €35M / 7% revenue |
| EU AI Act — GPAI | EU | Aug 2025 | >10^25 FLOP = systemic | €15M / 3% revenue |
| EU AI Act — High Risk | EU | Aug 2026 | Per category | €15M / 3% revenue |
| Colorado SB 24-205 | Colorado | Jun 30, 2026 | Consequential decisions | TBD |
| Texas TRAIGA | Texas | Sep 1, 2025 | Consequential decisions | Civil enforcement |
| CMMC 2.0 | Federal contractors | Dec 16, 2024 | CUI handling | Contract loss |
| BIPA | Illinois | Ongoing | Biometric data | $1-5K per violation |
| TAKE IT DOWN Act | Federal | May 19, 2025 | 1M+ MAU platform | FTC enforcement |
| GDPR | EU | Ongoing | EU personal data | €20M / 4% revenue |
| CCPA/CPRA | California | Ongoing | 100K CA residents OR $25M revenue | $7,500/intentional violation |
