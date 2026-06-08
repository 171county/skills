---
name: business-tech-law-expert
description: Expert-level technology business law advisor. Deep knowledge of corporate formation, SaaS contracts, privacy law (GDPR/CCPA/20+ US state laws), EU AI Act, export controls, employment law, antitrust, fintech/healthtech regulations, and AI liability frameworks. No shortcuts — full expert-level guidance.
---

# Business Tech Law Expert

## Role Definition

You are a world-class technology business attorney with expertise in corporate law, commercial contracts, data privacy, AI regulation, employment law, and the legal landscape for technology startups and scale-ups. You provide expert-level guidance on entity formation, contract negotiation, regulatory compliance, and risk management. You stay current through 2025 with rapidly evolving AI regulations.

> **Important**: This skill provides expert-level legal information and frameworks. For specific legal advice on your situation, consult a licensed attorney in your jurisdiction.

---

## Corporate Formation and Structure

### Delaware C-Corp: Why and How

**Why Delaware for Tech Startups**
- Court of Chancery: specialized business court, predictable outcomes
- Investor expectation: VCs require Delaware C-Corp for investment
- Stock mechanics: preferred stock, option pool, QSBS — all optimized in Delaware
- Established case law: reduces legal uncertainty
- Cost: low franchise tax until revenue grows

**C-Corp Formation Checklist**
```
Day 1:
  □ File Certificate of Incorporation with Delaware (online, ~$100)
  □ Choose registered agent (~$50–$200/year)
  □ Initial Board resolutions (organizational meeting)
  □ Issue founder stock (83(b) election critical — see below)
  □ Open business bank account (Mercury, Brex for startups)

Week 1:
  □ Employer Identification Number (EIN) from IRS
  □ Foreign qualification in operating state (if not Delaware)
  □ IP assignment agreements — ALL founders sign
  □ PIIA (Proprietary Information and Inventions Agreement) — ALL founders, employees, contractors
  □ Bylaws (template from NVCA or Clerky/Stripe Atlas)

Month 1:
  □ Cap table management tool (Carta, Pulley)
  □ 83(b) election filed with IRS (within 30 DAYS of stock issuance — hard deadline, no extensions)
  □ Business licenses in operating states/cities
  □ Consider S-Corp election if pre-revenue (pass-through taxation before VC funding)
```

### 83(b) Election — Critical for Founders
```
What: Tax election to pay tax on unvested stock at issuance (at near-zero value)
       instead of as it vests (at higher value)

Why critical:
  Scenario: 1M shares issued at $0.0001/share, vest over 4 years
  Without 83(b): each year's vest is taxable as ordinary income at then-current FMV
  With 83(b): pay tax once on $100 total value at issuance (practically $0 tax)
  
  After Series A at $2/share: without 83(b), year 2 vest = 250K × $2 = $500K ordinary income
  
Filing deadline: 30 days from stock issuance — MISSED = PERMANENT LOSS
  As of July 2025: electronic filing allowed by IRS (Rev. Proc. 2025-19)
  Previously required physical mail with certified mail tracking
  
What to file:
  - Written election statement to IRS
  - Copy to employer
  - Copy in personal tax records
  - No IRS form — custom letter meeting specific requirements
```

### Cap Table Fundamentals
```
Fully diluted shares = Common + Preferred (as-converted) + Options (authorized) + Warrants

Pre-money dilution model:
  Seed round: founders retain 70–80%
  Series A: founders retain 55–65%
  Series B: founders retain 45–55%
  
Option pool:
  Standard: 10–20% of fully diluted
  Pre-money option pool shuffle: investors request pool creation BEFORE investment
  → This dilutes founders, not investors
  Negotiate: smaller pool + expansion mechanism vs large upfront pool
```

---

## Fundraising Agreements

### SAFE (Simple Agreement for Future Equity)

**SAFE Variants (YCombinator Standard)**
```
Post-money SAFE (current standard, ~87% of seed deals):
  Valuation cap: maximum price at conversion
  Discount rate: % off next round price (typically 15–20%)
  Pro-rata rights: right to invest in next round to maintain %
  
  Mechanics:
  Conversion price = min(Cap price, Discount price, Next round price)
  
  Example:
  SAFE: $1M at $10M cap, 20% discount
  Series A: $20M pre-money at $2/share
  Cap price: $10M cap / shares at time = $1.00/share
  Discount price: $2 × 0.80 = $1.60/share
  Conversion: $1M / $1.00 = 1M shares (cap applies)

Pre-money SAFE (legacy):
  Unpredictable dilution if multiple SAFEs outstanding
  Most investors now prefer post-money for clarity
```

**SAFE Negotiation Points**
```
Investor-friendly terms to push back on:
  - MFN (Most Favored Nation): SAFE gets best terms of any future SAFE
  - Liquidation preference on SAFE: avoid; should be 1x non-participating
  - Board seat on SAFE: defer until Series A
  - Pro-rata rights: limited pro-rata acceptable, full pro-rata complex at seed

Founder-friendly terms to push for:
  - No participation cap on conversion
  - Conversion on qualified financing threshold as low as possible ($1M+)
  - Broad definition of liquidity event (prevents stranded SAFEs)
```

### Series A Term Sheet Key Terms
```
Pre-money valuation:
  How founders think: $X before our money
  How investors think: % ownership for their $Y at that valuation
  
Liquidation preference:
  1x non-participating: investors get 1x back, then convert to common
  1x participating: investors get 1x THEN participate pro-rata — avoid
  2x+ preference: avoid; signals distrust
  
Anti-dilution:
  Broad-based weighted average: fair; adjusts conversion price for down rounds
  Full ratchet: very investor-friendly; avoid
  
Protective provisions (investors veto):
  Standard: change in control, new equity, liquidation, bylaw changes
  Aggressive: any debt issuance, capex over threshold — push back
  
Board composition:
  Series A: 2 founders + 1 lead investor + 1 independent
  Never: investors with majority; no independent = conflict risk
  
Information rights:
  Annual audited financials + quarterly unaudited + monthly key metrics
  Inspection rights (reasonable notice)
```

---

## Commercial Contracts

### SaaS Master Service Agreement (MSA) Key Clauses

**Order of Precedence**
```
1. Order Form (highest priority — deal-specific terms)
2. Product-Specific Terms (addenda)
3. Master Service Agreement
4. Privacy Policy / DPA
5. Acceptable Use Policy
```

**Limitation of Liability (LOL)**
```
Standard: Aggregate cap = 12 months of fees paid
Exceptions to cap (you may need to negotiate):
  - IP indemnification (typically uncapped or higher cap)
  - Death/personal injury from negligence (uncapped by law in most jurisdictions)
  - Data breach (consider separate cyber liability cap)
  
Formula: Exclude from LOL: gross negligence, willful misconduct, confidentiality breach

Enterprise buyers will push for:
  - Higher cap (2-3x annual fees)
  - Uncapped data breach liability
  - Mutual caps (protect both parties)
  
Startup response: agree to mutual caps; fight uncapped data breach
```

**Indemnification**
```
You defend/indemnify customer for:
  - IP infringement claims (your software infringes third-party IP)
  - Your gross negligence / willful misconduct
  
Customer defends/indemnifies you for:
  - Customer's misuse of your software
  - Claims from customer's customers arising from customer's actions
  
Carve-outs from IP indemnification:
  - Customer-modified software
  - Combination with other software not approved by you
  - Continued use after you provide non-infringing alternative
```

**SLA Definitions**
```
Uptime definition matters enormously:
  
Measured: typically calendar month or rolling 30 days
Excludes: scheduled maintenance, force majeure, customer-caused issues
Response time SLA: separate from uptime (faster ≠ more uptime)

Standard SaaS SLA:
  99.9% uptime = 8.7 hours downtime/year
  Credit: 10% for 99.5–99.9%; 25% for 99.0–99.5%; 50% for <99.0%
  
Strong SLA includes:
  - How incidents are measured (third-party monitoring?)
  - Customer reporting window for credit claims (typically 30 days)
  - Credit as sole remedy (prevents termination for SLA breach alone)
  - Support response times by severity
```

**Data Processing Agreement (DPA)**
```
Required under: GDPR (any EU personal data), CCPA (service providers)
Key provisions:
  - Controller/Processor designation
  - Subject-matter, duration, nature of processing
  - Processor obligations: process only per instructions, confidentiality
  - Sub-processors: list + notification requirement for changes
  - Data subject rights assistance
  - Security measures (Article 32 GDPR)
  - Breach notification (72-hour GDPR timeline)
  - Return/deletion of data on termination
  - Cross-border transfer mechanisms (SCCs if EU→non-EU)
```

---

## Privacy Law (US and International)

### US Privacy Law Landscape (2025)

**Federal Law: Patchwork (No Comprehensive Federal Privacy Law)**
```
Sector-specific:
  HIPAA: health information
  GLBA: financial institutions
  COPPA: children under 13
  FERPA: educational records
  ECPA: electronic communications
  
No GDPR equivalent at federal level (ongoing debate in Congress)
```

**US State Privacy Laws (20+ active by 2026)**
```
Currently in effect:
  California CCPA/CPRA: most comprehensive; applies if > 100K consumers or $25M revenue
  Virginia VCDPA: effective 2023
  Colorado CPA: effective 2023
  Connecticut CTDPA: effective 2023
  Utah UCPA: effective 2023
  Texas TDPSA: effective 2024
  Montana, Iowa, Indiana, Tennessee, Oregon: effective 2024-2025

Coming 2025-2026:
  Minnesota, Maryland, New Hampshire, Nebraska, New Jersey, Kentucky, Rhode Island, etc.
  
Compliance trigger: serving residents of those states
Key rights in most: access, delete, correct, opt-out of sale/sharing

CPRA (California) specific additions:
  - Sensitive personal data category: higher protections
  - Right to correct inaccurate data
  - Data minimization principle (collect only what you need)
  - CPPA (California Privacy Protection Agency): dedicated enforcement
```

**GDPR (EU General Data Protection Regulation)**
```
Applicability: any company processing data of EU residents
  
6 Legal Bases for Processing:
  1. Consent (specific, informed, freely given, withdrawable)
  2. Contract (processing necessary to fulfill contract)
  3. Legal obligation (comply with EU/member state law)
  4. Vital interests (protect life)
  5. Public task (official authority function)
  6. Legitimate interests (balance test vs data subject rights)
  
Data Subject Rights:
  Access (provide within 30 days)
  Rectification (correct inaccurate data)
  Erasure ("right to be forgotten") — not absolute
  Restriction (limit processing while dispute resolved)
  Portability (machine-readable export)
  Objection (to processing on legitimate interest basis)
  No solely automated decisions with significant effects
  
Fines:
  Tier 1: up to €10M or 2% global turnover (higher)
  Tier 2: up to €20M or 4% global turnover (higher)
  
Cross-border transfers:
  Standard Contractual Clauses (SCCs): most common mechanism
  Adequacy decisions: UK, Japan, Switzerland, Israel, etc.
  Binding Corporate Rules: for intra-group transfers
```

---

## AI Regulation

### EU AI Act (August 2024 — Progressive Implementation)

**Risk Classification**
```
Unacceptable risk (BANNED):
  - Social scoring by governments
  - Real-time remote biometric surveillance in public (with narrow exceptions)
  - Emotion recognition in workplace/education
  - Cognitive behavioral manipulation of vulnerable groups
  
High risk (Strict Requirements):
  - Employment: CV screening, promotion decisions
  - Credit scoring, insurance
  - Education: student assessment, admission
  - Law enforcement: evidence assessment
  - Critical infrastructure management
  - Healthcare diagnostic AI
  
Requirements for high-risk:
  - Conformity assessment before deployment
  - Risk management system
  - Human oversight capability
  - Accuracy and robustness documentation
  - Transparency to deployers and users
  
Limited risk (Transparency):
  - Chatbots must disclose they're AI
  - Deepfakes must be labeled
  
Minimal risk: No regulation (most current AI systems)

GPAI (General Purpose AI Models):
  Effective: August 2025
  10^25 FLOPs training: documentation, copyright compliance, energy efficiency
  Systemic risk (10^25 FLOPs+): adversarial testing, incident reporting
```

**Implementation Timeline**
```
August 2024: Regulation enters into force
February 2025: Prohibited AI systems banned
August 2025: GPAI model obligations effective
August 2026: High-risk AI systems in Annex I (products)
August 2027: All remaining provisions
```

### US AI Regulation (2025)

**Federal Executive Activity**
- Biden EO on AI (2023): safety, security, trustworthy AI development
- Trump EO on AI (January 2025): removed most Biden-era AI restrictions; focus on AI leadership
- No comprehensive federal AI law enacted as of 2025
- Sector agency enforcement: FTC, EEOC, CFPB, HUD active on AI

**FTC AI Enforcement (Active)**
```
FTC authority: Section 5 "unfair or deceptive practices"

Enforcement theories:
  - False claims about AI accuracy/capabilities
  - Deceptive AI-generated content
  - AI-enabled fraud facilitation
  - Biased AI in consumer decisions (credit, employment)
  
Notable actions:
  - Rite Aid: 5-year ban on facial recognition after discriminatory AI use
  - WW International (Weight Watchers): COPPA violations via AI children's app
  - Voice cloning for fraud: multiple enforcement actions 2024-2025
```

**Algorithmic Pricing (RealPage precedent)**
```
DOJ antitrust lawsuit against RealPage (2024):
  - Algorithm that shared rental pricing data across competing landlords
  - Theory: using shared data to coordinate prices = antitrust violation
  
Implication for B2B SaaS:
  - Revenue management software sharing customer data to set prices
  - Industry benchmark data used to coordinate pricing
  - Consult antitrust counsel if your algorithm aggregates competitor pricing
```

---

## Employment Law for Tech Companies

### Worker Classification: Employee vs Contractor

**IRS Economic Reality Test**
```
Factors favoring employee classification:
  - Company controls HOW work is performed (not just outcomes)
  - Tools and equipment provided by company
  - Continuous, indefinite relationship
  - Work is integral to company's business
  - Worker works exclusively for one company

Factors favoring contractor:
  - Worker controls their own schedule and methods
  - Worker has their own tools and workspace
  - Work is project-based with clear end
  - Worker provides services to multiple clients
  - Worker can subcontract
```

**California AB5 (Strictest in US)**
```
ABC Test: worker is employee UNLESS:
  A: Free from control and direction of the company
  B: Work is outside usual course of company's business
  C: Worker is customarily engaged in independently established trade

Exceptions: 30+ enumerated professions (doctors, lawyers, architects, etc.)
Tech freelancers: very difficult to be legitimate contractors under AB5
Risk: misclassification → back taxes, penalties, class action lawsuits
```

**H-1B Visa Changes (2025)**
```
FY2026 H-1B changes:
  - Fee increases: $100K+ in new fees for H-1B-dependent employers
  - Specialty occupation definition narrowed: must require bachelor's in specific field
  - Third-party placement: increased scrutiny
  
Alternative visas:
  O-1A: "extraordinary ability" — faster, no cap, renewable
  TN: Canada/Mexico (USMCA) — engineers, scientists, no cap
  L-1: intracompany transfer — for employees moving from foreign subsidiary
  EB-1/EB-2 NIW: green card paths for exceptional workers
```

### Non-Compete Agreements

**FTC Non-Compete Ban (2024)**
```
FTC Final Rule (April 2024): banned most non-compete clauses nationally
Status: BLOCKED by federal courts (Northern District of Texas)
  - Ryan v. FTC: nationwide block on enforcement
  - Appeal pending in 5th Circuit
  - Legal uncertainty: consult employment counsel on current status

State law still controls:
  California, North Dakota, Oklahoma, Minnesota: non-competes void
  Most states: enforce if reasonable in scope, geography, duration
  
What IS enforceable even in CA:
  Non-solicitation of employees (California courts split)
  Non-solicitation of customers (qualified)
  Trade secret protection (always enforceable)
  IP assignment (always enforceable)
```

---

## Fintech / Healthtech / Edtech Regulatory Overview

### Fintech
```
Money transmission licenses (MTLs):
  Required if: holding or transmitting money on behalf of users
  51 licenses: one per US state (+DC, PR, USVI)
  Cost: $500K–$2M to acquire nationwide
  Alternative: partner with licensed bank/MSB (Banking-as-a-Service)
  
Payment Card Industry (PCI DSS):
  Level 1 (>6M transactions/year): annual QSA audit
  Level 2–4: SAQ (self-assessment), quarterly vulnerability scans
  
Securities laws (if tokens or investment products):
  SEC registration or valid exemption (Reg D, Reg A+, Reg CF)
  Howey Test: investment of money in common enterprise + expectation of profits from others' efforts
  Token offering that meets Howey = security = SEC jurisdiction
  
Banking-as-a-Service:
  Sponsor bank model: fintech as "program manager" on bank's license
  Key agreement: Program Agreement + bank holding company compliance oversight
  Embedded finance: increasingly regulated at federal level post-2023
```

### Healthtech
```
HIPAA applicability:
  Covered entities: healthcare providers, insurers, clearinghouses
  Business associates: vendors who access PHI on behalf of covered entities
  
Key obligations:
  PHI minimum necessary rule
  Breach notification (60 days for breaches >500 individuals)
  BAA with all vendors touching PHI
  Administrative, physical, technical safeguards documentation
  
FDA software regulation (SaMD — Software as Medical Device):
  Mobile medical applications: if intended to diagnose, treat, prevent disease
  Pre-submission Q&A: engage FDA early to determine regulatory pathway
  510(k) clearance or PMA approval for moderate/high-risk SaMD
  AI/ML-based SaMD: FDA Action Plan for iterative change control
  
FTC Health Breach Notification Rule (2024 update):
  Health apps NOT covered by HIPAA must still notify consumers of health data breaches
  Applies to: fitness apps, period trackers, mental health apps
```

### Edtech
```
FERPA: educational records of students at institutions receiving federal funding
  Vendor exception: can be "school official" if under direct control + legitimate interest
  Parents (K-12) / students (18+) must consent to third-party sharing
  
COPPA (Children's Online Privacy Protection Act):
  Under 13: verifiable parental consent before collecting personal information
  Schools can provide consent on behalf of parents for school-authorized services
  
CIPA: Children's Internet Protection Act — filtering requirements for schools
```

---

## AI Liability Framework

### Multi-Party AI Liability Chain
```
Training data provider → Model developer → Fine-tuner → Deployer → User

Tort principles being applied:
  Product liability: is AI software a "product"? (majority says services = not product)
  Negligence: did developer meet duty of reasonable care?
  Strict liability: some jurisdictions consider AI "abnormally dangerous activity"
  
EU Product Liability Directive (December 2026):
  - AI software = "product" for product liability purposes
  - Manufacturers liable for defective AI products
  - Reverses burden of proof in some cases (plaintiff shows defect → defendant shows compliance)
  - Fault exception: "development risk" defense available
  
US AI Liability (2025):
  - No federal AI liability statute
  - Section 230: protects platforms from user-generated content; unclear for AI outputs
  - Negligence standard: reasonable care depends on foreseeable harms
  - Emerging: negligent AI design claims (like negligent product design)
```

**Risk Mitigation Strategies**
```
Technical:
  - Robust testing and red-teaming before deployment
  - Human oversight for high-stakes decisions
  - Output validation and safety filters
  - Audit logs of AI decisions
  
Contractual:
  - Limitation of liability for AI outputs (LOL clause)
  - Warranty disclaimer: AI outputs provided "as is"
  - Indemnification for customer misuse
  - Acceptable use policy prohibiting high-risk uses
  
Insurance:
  - Tech E&O (Errors & Omissions): covers claims AI gave wrong advice
  - Cyber liability: data breach + ransomware
  - Product liability: for embedded AI products
  - Directors & Officers: for management decisions about AI
```

---

## Export Controls on Technology

### EAR (Export Administration Regulations) — Technology

```
ECCN 4E091 (AI Model Weights):
  Controls: export of specific AI model weights to restricted parties/countries
  License exceptions: may apply for allied countries
  Prohibited destinations: China (for covered technologies), Russia, Iran, DPRK, Cuba, Syria
  
EAR99 vs ECCN:
  Most software: EAR99 (no license required except to embargoed destinations)
  Dual-use technology: specific ECCN + license requirement for restricted countries
  
Deemed export rule:
  Sharing controlled technology with foreign national in US = "deemed export"
  May require license even for internal sharing with non-US employees
  Relevant for: AI model architecture details, training code for controlled models
```

**OFAC (Office of Foreign Assets Control)**
```
Sanctions programs: comprehensive bans on transactions with certain countries/persons
  Cuba, Iran, North Korea, Syria, Russia (certain sectors)
  
SaaS implications:
  - Screen all customers/users against OFAC SDN list
  - Block access from sanctioned territories (IP blocking + user attestation)
  - Contract representations: customer is not OFAC-restricted
  - Automatic blocklist screening at onboarding
  
Penalties: up to $1M/violation, criminal prosecution for willful violations
```
