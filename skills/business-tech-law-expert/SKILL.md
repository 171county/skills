---
name: business-tech-law-expert
description: Expert-level business and technology law knowledge covering company formation (Delaware C-Corp vs LLC), contracts (MSA, SOW, NDA, ToS, DPA), data privacy (GDPR, CCPA/CPRA, COPPA, HIPAA, US state laws), AI regulation (EU AI Act, US sector-specific rules), employment law (classification, non-competes, equity compensation, 409A), SaaS legal architecture, securities law (Reg D, Reg CF, SAFEs), open source compliance, international business law, litigation/ADR, and M&A for tech companies. Use for any legal question involving a tech startup or established tech business.
---

# Business Tech Law Expert

> **Disclaimer:** Educational reference only. Technology law is complex, jurisdiction-specific, and rapidly evolving. Always consult qualified legal counsel for specific matters. No attorney-client relationship is created.

---

## Company Formation

### Delaware C-Corp vs. LLC

**Why 93% of VC-backed startups choose Delaware C-Corps:**

| Factor | Delaware C-Corp | LLC |
|--------|----------------|-----|
| VC investment | Preferred/required | Problematic (tax pass-through complications for institutional LPs) |
| Multiple share classes | Yes (Common + Preferred) | Complicated/impractical |
| ISOs (Incentive Stock Options) | Yes | No |
| QSBS eligibility (§ 1202) | Yes (C-corp required) | No |
| Double taxation | Yes (federal + dividends) | No (pass-through) |
| Equity compensation | Robust (options, RSUs, warrants) | Cumbersome |
| Acquisition readiness | High | Lower; conversion required |
| Delaware case law | Extensive | Less developed |

**When LLC makes sense:** Bootstrapped businesses not seeking VC; real estate/asset-holding; professional service firms; businesses prioritizing pass-through taxation.

**QSBS (Section 1202) note:** Post-OBBBA 2025, founders holding qualifying C-Corp stock for ≥5 years may exclude up to **$15 million** of gain from federal income tax at exit. Major incentive for Delaware C-Corp.

### Incorporation Process

1. **File Certificate of Incorporation** with Delaware Division of Corporations (~$89–$200 filing fee)
2. **Authorized shares:** Typical: 10M shares authorized. Use **Assumed Par Value Method** for franchise tax (not Authorized Shares Method) to avoid $200K+ annual tax bills
3. **Appoint registered agent** in Delaware (~$50–$300/year)
4. **Issue organizational resolutions** (elect officers, authorize bank accounts, approve bylaws)
5. **Issue founder shares** at par value ($0.0001/share typical)
6. **File 83(b) elections** within **30 days** of restricted stock grant—no exceptions
7. **Obtain EIN** via IRS Form SS-4
8. **File foreign qualification** in any state where you conduct business (typically home state)
9. **Annual Delaware franchise tax** due March 1; can be $50 or $200K+ depending on method

**Platform tools:** Stripe Atlas, Clerky, Gust Launch for automation. Complex cap tables warrant Cooley, Wilson Sonsini, Gunderson, Orrick, or Fenwick.

### Founder Equity and Vesting

**Standard schedule: 4-year / 1-year cliff:**
- Months 1–11: No vesting
- Month 12: 25% vests (the "cliff")
- Months 13–48: Remaining 75% vests monthly (1/48 per month)
- Example: 1M shares; month 12 → 250K vest; months 13–48 → 20,833/month

**Acceleration provisions:**
- **Single-trigger:** Accelerates upon change of control alone (acquirers resist)
- **Double-trigger (market standard):** Requires BOTH change of control AND involuntary termination
- Typical acceleration amount: 12–24 months of unvested shares

**2024 data:** 45.9% of 2-founder startups use 50/50 splits; 89% of seed-stage term sheets require standard vesting.

### The 83(b) Election

File with IRS within **30 days of restricted stock grant—no extensions, no exceptions.**

- Elect to recognize income at grant (when FMV is minimal) vs. at vesting
- Converts future appreciation from ordinary income → capital gains
- IRS Form 15620 (released 2024) is now the official form
- For founders at $0.0001/share par value, taxable income is negligible
- Missing this window can cost founders millions in ordinary income taxes at exit

---

## Contracts and Agreements

### Master Service Agreements (MSA)

Umbrella contract governing ongoing relationships; specific work defined in SOWs underneath.

**Core MSA components:**
- **Definitions:** "Services," "Deliverables," "Confidential Information"
- **IP ownership:** Work-for-hire language; deliverable assignment; license to vendor tools
- **Confidentiality:** 3–5 years typical; perpetual for trade secrets
- **Reps and warranties:** Authority, non-infringement, compliance with laws
- **Indemnification:** IP indemnity (vendor indemnifies customer for third-party IP claims)
- **Limitation of liability:** Cap (typically 1–12× fees paid in prior 12 months); carve-outs for fraud, gross negligence, IP indemnity, data breach, confidentiality
- **Term and termination:** For convenience (with notice) + for cause (material breach + cure period)
- **Data Protection Addendum:** Required for any processing of personal data
- **Governing law and dispute resolution**

**Negotiation hot zones:**
- Liability cap: Customers push uncapped IP and data breach; vendors resist
- Indemnification: Mutual vs. vendor-only
- Assignment: Rights assignable to acquirers without consent

### Non-Disclosure Agreements (NDA)

**One-way NDA:** Only one party's information protected (startup disclosing to investor; most early-stage VCs won't sign).

**Mutual NDA:** Both parties protected; used for partnerships, vendor discussions, M&A conversations.

**Critical provisions:**
- **Definition of Confidential Information:** Broad but with carve-outs: (1) public domain, (2) independently developed, (3) received from third party without restriction, (4) required by law
- **Standard of care:** "Reasonable care" or "same care as own confidential information, but no less than reasonable"
- **Residuals clause:** Watch for this loophole—allows use of information "retained in unaided memory"; effectively lets employees use your trade secrets after departure
- **Duration:** 2–5 years typical; trade secrets should be perpetual
- **Injunctive relief:** Standard clause acknowledging money damages are inadequate

**Practical note:** NDAs are rarely enforced in court; proof of misappropriation is difficult. Primary value is psychological deterrence and as a predicate for trade secret claims.

### Terms of Service (ToS)

**Clickwrap** (checkbox + "I agree" with hyperlink) is enforceable in virtually all courts. **Browsewrap** (footer link only) often is not enforced against consumers.

**Core ToS components for SaaS:**
1. Acceptance mechanism (clickwrap)
2. License grant (limited, non-exclusive, non-transferable, non-sublicensable)
3. Account security obligations
4. Acceptable Use Policy (AUP)
5. IP ownership
6. Payment terms
7. Data collection (reference Privacy Policy)
8. Disclaimer of warranties ("AS IS," "AS AVAILABLE")
9. Limitation of liability (excluding consequential/punitive; cap = fees paid)
10. Indemnification by user
11. **Class action and jury trial waivers** (enforceable under FAA; see below)
12. Arbitration agreement
13. Governing law and venue
14. Right to unilaterally modify with notice

### Data Processing Agreements (DPA)

**Mandatory under GDPR Article 28** for any controller-processor relationship.

**Nine required elements:**
1. Subject matter, duration, nature, and purpose of processing
2. Types of personal data and categories of data subjects
3. Process only on documented controller instructions
4. Personnel bound by confidentiality
5. Appropriate security measures (Article 32)
6. Subprocessors only with controller consent + flow-down obligations
7. Assist controller with data subject rights requests
8. Assist with breach notifications and DPIAs
9. Delete or return data upon contract termination

**Practical note:** Most SaaS companies are **processors** when customers use the platform for their users' data. DPAs are procurement requirements—large enterprise customers will not sign without one. Fines for missing required DPA: up to **€10 million or 2% of global annual turnover**.

---

## Data Privacy Law

### GDPR

**Regulation:** (EU) 2016/679; effective May 25, 2018  
**Territorial scope:** Applies globally to any organization processing EU/EEA residents' personal data

**Key principles (Article 5):**
1. Lawfulness, fairness, transparency
2. Purpose limitation
3. Data minimization
4. Accuracy
5. Storage limitation
6. Integrity and confidentiality (security)
7. Accountability

**Lawful bases for processing (Article 6):**
- **Consent:** Freely given, specific, informed, unambiguous; must be opt-in
- **Contract:** Necessary to perform a contract with the data subject
- **Legal obligation**
- **Vital interests**
- **Public task**
- **Legitimate interests:** Balancing test; frequently used for B2B marketing

**Special categories (Article 9):** Health, biometric, genetic, racial/ethnic origin, political opinions, religious beliefs, sexual orientation—require explicit consent or specific exemption.

**Data Subject Rights:**
- Right of access (Article 15) — respond within 1 month
- Right to rectification (Article 16)
- Right to erasure/"right to be forgotten" (Article 17)
- Right to restriction of processing (Article 18)
- Right to data portability (Article 20)
- Right to object (Article 21) — applies to legitimate interests and direct marketing
- Rights related to automated decision-making (Article 22) — no solely automated decisions with legal/significant effects without human review option

**Cross-border transfers (Chapter V):**
- EU-US Data Privacy Framework (DPF): Adopted July 2023; already facing legal challenge (Schrems III expected)
- Standard Contractual Clauses (SCCs): Updated June 2021 versions required
- Binding Corporate Rules (BCR): Intra-group transfers; lengthy DPA approval process

**Enforcement penalties:**
- Tier 1 fines: Up to **€10M or 2% of global annual turnover**
- Tier 2 fines: Up to **€20M or 4% of global annual turnover**
- Cumulative GDPR fines: €5.88 billion since 2018; €1.2 billion in 2024 alone

### CCPA/CPRA (California)

**Who must comply:** For-profit businesses collecting California consumers' personal information that meet ANY one of:
- Annual gross revenue > $25 million
- Buy, sell, or share personal information of 100,000+ consumers or households
- Derive 50%+ of annual revenue from selling consumers' PI

**Consumer rights:** Know, delete, correct, opt-out of sale/sharing, limit use of SPI, non-discrimination, portability.

**"Sale" definition:** Broadly includes sharing for cross-context behavioral advertising, even without monetary exchange.

**GPC (Global Privacy Control):** California, Colorado, Connecticut, Texas now require businesses to honor GPC browser signals as automatic opt-outs.

**CPPA enforcement:** Independent agency; $100M+ in enforcement actions in 2024; 30-day cure period eliminated December 31, 2024.

### US State Privacy Law Landscape

| State | Effective | Key Distinction |
|-------|-----------|-----------------|
| California (CCPA/CPRA) | Jan 2023 | CPPA enforcement; GPC required; most comprehensive |
| Virginia (VCDPA) | Jan 2023 | Permanent 30-day cure; no private right of action; $7,500/violation |
| Colorado (CPA) | July 2023 | $20K max fine; GPC required since July 2024 |
| Connecticut (CTDPA) | July 2023 | GPC required; 60-day cure |
| Texas (TDPSA) | July 2024 | **Broadest scope**—no revenue threshold; applies to any non-small-business; GPC required Jan 2025 |
| Oregon (OCPA) | July 2024 | Universal opt-out required |
| Iowa (ICDPA) | Jan 2025 | Business-friendly; 90-day cure |

**As of 2025: 20+ states have comprehensive privacy laws.** A unified data map, purpose limitation framework, and honor-all-state-rights mechanism is required for patchwork compliance.

### COPPA (Children's Online Privacy Protection Act)

**Statute:** 15 U.S.C. § 6501; 16 CFR Part 312  
**2025 Amendments effective April 22, 2025; compliance deadline April 22, 2026**

**Applicability:** Sites/services directed to children under 13, or with actual knowledge of collecting data from children under 13.

**Core obligations:** Verifiable parental consent before collecting PI from children; privacy notice to parents; right to review/delete child's info; reasonable data security.

**2025 COPPA updates:**
- **Opt-in required for targeted advertising**—cannot monetize children's data without affirmative parental permission
- **Biometric identifiers** added to definition of personal information (fingerprints, facial recognition, voiceprints, gait)
- **Data retention limits:** Retain only as long as necessary for the specific purpose
- Enforcement: Google/YouTube $170M (2019), Epic Games (Fortnite) $520M (2022)

### HIPAA

**Business Associates (BA):** Any entity that creates, receives, maintains, or transmits PHI on behalf of a covered entity—**includes SaaS companies, cloud storage providers, analytics platforms.**

**Business Associate Agreement (BAA):** Mandatory contract; BAs have **direct HIPAA liability**; must include permitted uses, security obligations, breach notification requirements, subcontractor requirements, data destruction at termination.

**Breach notification timeline:** BAs must notify covered entity within **60 days** of discovering a breach; breaches affecting 500+ residents must notify local media and HHS.

### Data Breach Notification

| Jurisdiction | Timeline | To Whom |
|-------------|----------|---------|
| GDPR | 72 hours (supervisory authority) | DPA; individuals "without undue delay" if high risk |
| CCPA/CPRA | 30 days to consumers; 15 days to AG if 500+ | Consumers + AG |
| HIPAA | 60 days | CE → Individuals + HHS |
| SEC (public companies) | 4 business days of materiality determination | SEC Form 8-K |
| All 50 US states | 30 days or "without unreasonable delay" (varies) | State AG + affected residents |

---

## AI Regulation

### EU AI Act

**Regulation:** (EU) 2024/1689; entered into force August 1, 2024

**Implementation timeline:**

| Date | Obligation |
|------|-----------|
| **Feb 2, 2025** | Prohibited AI practices banned; AI literacy obligations |
| **Aug 2, 2025** | GPAI model obligations; AI Office active |
| **Aug 2, 2026** | High-risk AI system obligations fully apply |
| **Aug 2, 2027** | High-risk AI under Annexes I (existing products) |

**Risk classification:**

**Prohibited (effective Feb 2, 2025):**
- Social scoring by government entities
- Real-time remote biometric ID in public spaces (law enforcement)
- Subliminal manipulation techniques
- Emotion recognition in workplace/education (with exceptions)
- Untargeted facial recognition database scraping

**High-Risk AI (effective Aug 2, 2026):** Employment (CV screening), essential services (credit scoring), law enforcement, migration/asylum, administration of justice, education, critical infrastructure.

**High-Risk obligations:** Risk management system, data governance, technical documentation, automatic logging, transparency, human oversight, accuracy/robustness/cybersecurity, EU database registration.

**GPAI models (effective Aug 2, 2025):** Technical documentation, EU copyright compliance policy, training data summary. For systemic risk (≥10^25 FLOPs): adversarial testing, incident reporting, cybersecurity, energy efficiency disclosure.

**Penalties:**
- Prohibited practices: **€35M or 7% of global annual turnover**
- High-risk violations: **€15M or 3%**
- Incorrect information: **€7.5M or 1.5%**

### US AI Regulation (2025)

**Executive Order 14179 (Trump, Jan 2025):** Revoked Biden-era AI EO (14110); directs agencies to promote US AI leadership, reduce regulatory barriers.

**Sector-specific rules remain in force:**
- Healthcare: FDA guidance on AI/ML-based SaMD (software as medical device)
- Finance: OCC/Fed/FDIC model risk management (SR 11-7); CFPB adverse action notice requirements for AI credit decisions
- Hiring: EEOC guidance on AI in employment (Title VII disparate impact liability)
- Housing: HUD guidance on algorithmic pricing tools (Fair Housing Act)

**NIST AI RMF 1.0:** GOVERN → MAP → MEASURE → MANAGE. Generative AI Profile (NIST AI 600-1, July 2024) addresses 12 risk categories including confabulation, data privacy, harmful bias, IP, information security. Foundation for ISO/IEC 42001 compliance and EU AI Act technical documentation.

---

## Employment Law

### Employee vs. Independent Contractor

**Three tests in use:**

**1. IRS Common Law Test (federal tax):**
- Behavioral control (how work is performed)
- Financial control (economic dependence)
- Type of relationship (permanency, benefits, integration)

**2. Economic Reality Test (FLSA):**
- Permanency, degree of control, profit/loss opportunity, investment in tools, integral to business, skill required

**3. ABC Test (California AB 5—most worker-protective):**
All three prongs must be satisfied for IC classification:
- **A:** Worker is free from company's control (in fact AND contract)
- **B:** Worker performs work **outside** the usual course of company's business
- **C:** Worker is customarily engaged in an independently established trade/occupation

Failing ONE prong makes the worker a legal employee—regardless of any signed 1099 agreement.

**California penalties:** Willful misclassification: $5K–$15K per violation; pattern/practice: $25K per violation; PAGA (Private Attorney General Act) stacking.

### Equity Compensation

**ISOs (Incentive Stock Options):**
- Employees only
- Strike price must equal FMV at grant date (requires 409A valuation)
- No ordinary income on exercise (but AMT preference item)
- Qualifying disposition (≥2 years from grant, ≥1 year from exercise) → capital gains
- Annual ISO limit: $100K value can vest per year; excess = NSO
- IRC § 422

**NSOs (Non-Qualified Stock Options):**
- Employees, contractors, directors, advisors
- Ordinary income recognized on exercise (spread = FMV - exercise price)
- Company gets compensation deduction
- IRC § 83

**RSUs (Restricted Stock Units):**
- Common at later-stage and public companies
- No 83(b) election available
- Taxed as ordinary income on vesting
- Double-trigger RSUs (time + liquidity event) = tax-efficient for private companies

**409A Valuation:**
- Independent third-party appraisal of common stock FMV
- Required to set ISO/NSO strike prices (safe harbor protection)
- Must be refreshed every 12 months, or within 90 days of material events (new funding round, acquisition offer, secondary transaction at materially different price)

### Non-Competes (2025)

FTC's April 2024 nationwide ban was struck down August 2024 (*Ryan LLC v. FTC*). FTC dismissed appeals September 2025. **State law governs.**

| State | Status |
|-------|--------|
| California | **Void**; AB 1076 requires notifying employees of void agreements; employees can sue employers who seek to enforce |
| Minnesota | Banned Jan 1, 2023 |
| Colorado | Enforceable only for workers earning ≥$123,750 (2024 threshold) |
| Washington | Enforceable only for employees earning >$100K+; max 18 months; must pay salary during restriction period |
| New York, Texas, Florida, Delaware | Common law reasonableness test |

**Alternatives:** NDAs (protect specific trade secrets), non-solicitation agreements (more widely upheld), garden leave clauses, trade secret protections.

---

## SaaS Legal Architecture

### Contract Stack

```
ORDER FORM / QUOTE (pricing, seats, term)
      ↓ governed by
MASTER SUBSCRIPTION AGREEMENT (MSA)
  - License grant
  - Payment terms
  - IP ownership
  - Liability limits
  - Term and termination
      ↓ incorporates
SLA        |    AUP          |    DPA
Uptime %   |    Prohibited   |    GDPR Art. 28
Credits    |    uses         |    Subprocessors
```

### License Grant Clause

> "Subject to the terms and conditions of this Agreement and timely payment of all fees, Provider grants Customer a non-exclusive, non-transferable, non-sublicensable, worldwide, limited license to access and use the Service during the Subscription Term solely for Customer's internal business purposes and in accordance with the Documentation and any usage limits set forth in the applicable Order Form."

Key elements: non-exclusive (vendor can license others), non-transferable (cannot assign), internal business purposes (prevents resale), usage limits in Order Form.

### Limitation of Liability Structure

1. **Exclusion of consequential damages:** Neither party liable for lost profits, lost revenue, lost data, indirect, incidental, special, punitive damages
2. **Aggregate cap:** Total liability = fees paid in prior 12 months
3. **Carve-outs (negotiate these):** IP indemnification, confidentiality breach, data breach, fraud, death/personal injury

Market standard 2024: Cap = 12 months fees; carve-outs for fraud, death/PI, IP indemnity, data breach (at 2× or uncapped).

### Data Ownership Clause

> "As between the parties, Customer retains all right, title, and interest in and to Customer Data. Customer grants Provider a limited, non-exclusive, worldwide license to process Customer Data solely to provide the Service..."

**Watch for:** Broad license grants allowing vendor to use customer data for model training, product improvement, or anonymized analytics—negotiate to require explicit consent or opt-out.

### SLA Terms

- **Uptime metrics:** 99.9% = 8.7 hrs downtime/year; 99.95% = 4.4 hrs; 99.99% = 52 mins
- **Measurement:** Monthly is better for customer (annual hides outage periods)
- **Exclusions:** Scheduled maintenance, customer-caused downtime, third-party infrastructure
- **Credits:** Typical: 5–25% of monthly fee per SLA breach
- **Sole remedy clause:** SLA credits are "sole and exclusive remedy" for downtime—prevents breach of contract claim

### Acceptable Use Policy (AUP)

Core prohibited uses:
- Illegal activities
- Spam, phishing, malware distribution
- Unauthorized scraping or automated data collection
- Reverse engineering or decompilation
- Reselling or sublicensing
- Activities violating third-party IP or privacy rights
- ITAR/EAR export control violations

Enforcement: Right to suspend or terminate immediately for AUP violations; no refund; customer indemnification.

---

## Securities Law

### Regulation D Overview

Reg D provides exemptions from SEC registration for private placements. Failure = **rescission rights for investors** (they can demand money back).

**Rule 506(b) (the workhorse):**
- No general solicitation
- Up to 35 non-accredited but sophisticated investors (unlimited accredited)
- Self-certification of accredited status
- File **Form D with SEC within 15 days** of first sale
- $170 billion raised in FY2024

**Rule 506(c) (general solicitation):**
- General solicitation and advertising PERMITTED
- ALL investors must be accredited—no exceptions
- **Independent verification required** (review tax returns/bank statements/CPA certification; or third-party service)
- Only $12 billion raised in FY2024 (verification burden limits adoption)

**Accredited investor definition (updated 2020):**
- Individual: Net worth >$1M (excluding primary residence) OR income >$200K ($300K joint) for 2 years; OR professional credentials (Series 7, 65, 82); OR "knowledgeable employee"
- Entity: Assets >$5M OR all equity owners accredited OR registered investment adviser/broker-dealer

**Regulation Crowdfunding (Reg CF):**
- Up to **$5 million** in 12 months
- Must use SEC-registered funding portal or broker-dealer
- File Form C (disclosure statement)
- Securities subject to 1-year transfer restriction
- Investor limits based on income/net worth

---

## Open Source Compliance

### The License Risk Hierarchy for SaaS

| License | SaaS Risk |
|---------|----------|
| MIT, Apache 2.0, BSD, ISC | None—permissive |
| LGPL | Low—library mods must be open-sourced; linked code can stay proprietary |
| MPL 2.0 | Low—file-level copyleft; works with proper separation |
| GPL v2/v3 | Medium—SaaS loophole exists (no distribution trigger), but distribution of GPL code triggers release |
| **AGPL v3** | **High—closes SaaS loophole; using as network service triggers source disclosure** |
| **SSPL** | **Very High—requires open-sourcing entire service stack** |
| BSL 1.1 | Variable—production use restricted to licensor permission |

### SBOM (Software Bill of Materials)

**Regulatory drivers:**
- Executive Order 14028 (Biden, May 2021): Federal agencies must require SBOMs from software vendors
- FDA (2023): Medical device software must include SBOM
- EU Cyber Resilience Act (CRA): Requires SBOM; enforcement 2027

**Formats:** SPDX (ISO/IEC 5962:2021), CycloneDX (OWASP), SWID Tags (ISO/IEC 19770-2)

**NTIA minimum elements:** Supplier name, component name, version, identifier (PURL), dependency relationships, SBOM author, timestamp.

**Tools:** Syft, Grype, FOSSA, Snyk, Black Duck for automated SCA and license compliance scanning. Integrate into CI/CD pipeline.

### Enterprise Contract Provisions for Open Source

Negotiate these in enterprise agreements:
- Vendor must disclose open source components and licenses used
- Vendor warrants compliance with all open source license terms
- **AGPL/GPL prohibition:** Vendor warrants no AGPL/GPL code is distributed to customer in a manner that would impose open source obligations on customer's proprietary software
- IP indemnification covers open source IP claims
- SBOM delivery upon request
- No "contamination" of customer's codebase

---

## International Business Law

### US Companies Doing Business in EU

**Key requirements:**
- **GDPR compliance:** Any EU customer data processing
- **EU Representative (GDPR Art. 27):** If you process EU residents' data without EU establishment and aren't exempt (small scale, occasional, low risk)
- **EU AI Act:** If AI system is deployed or used by EU users
- **VAT registration:** Required for B2C sales to EU consumers; VAT OSS scheme simplifies multi-country registration

**Data transfer mechanisms (post-Schrems II):**
1. EU-US Data Privacy Framework (DPF): July 2023; already facing challenge
2. Standard Contractual Clauses (SCCs): Updated June 2021 versions required
3. Binding Corporate Rules (BCR): For intra-group transfers
4. Adequacy decisions: UK, Switzerland, Canada, Israel, Japan, etc.

### Export Controls (ITAR and EAR)

**ITAR (International Traffic in Arms Regulations):**
- Administered by State Department / DDTC
- Covers defense articles/services on the US Munitions List (USML)
- **Deemed export rule:** Sharing ITAR-controlled technology with foreign nationals (even inside the US) requires license
- Penalties: Criminal (up to $1M/violation, 20 years prison); civil (up to $1.3M/violation)

**EAR (Export Administration Regulations):**
- Administered by Bureau of Industry and Security (BIS), Department of Commerce
- Covers dual-use items on Commerce Control List (CCL)
- Most software = **EAR99** (no license required except to embargoed countries/sanctioned parties)
- Encryption software: ECCN 5D002 or 5E002

**OFAC Sanctions compliance:**
- Screen all customers against SDN (Specially Designated Nationals) list
- Comprehensive embargoes: Cuba, Iran, North Korea, Syria, Crimea/specified Russia/Ukraine regions
- Geo-block embargoed countries in SaaS

**Tech startup export compliance checklist:**
1. Determine if products are subject to ITAR or EAR (or EAR99)
2. Implement Export Control Compliance Program
3. Screen customers/partners against denied party lists (BIS Entity List, OFAC SDN)
4. Manage deemed export risks for foreign national employees
5. Ensure ToS restricts use in embargoed jurisdictions

---

## Litigation and ADR

### Alternative Dispute Resolution

**Mediation:** Non-binding; mediator facilitates settlement; confidential under FRE 408; low cost; typically prerequisite to arbitration.

**Arbitration:** Binding; generally faster than litigation (12–18 months vs. 3–5 years); private; limited appeals (Federal Arbitration Act § 10—arbitrator errors of law rarely grounds for vacatur).

**AAA Commercial Arbitration Rules:** Most common for B2B tech contracts.

**Mass Arbitration (2024):**
- AAA adopted Mass Arbitration Supplementary Rules in 2024 (≥25 similar demands filed against same respondent)
- Class action waivers + mandatory arbitration can expose to coordinated mass filings costing MORE than class actions (AAA filing fee $1,750+ per claim × thousands of claimants)
- Design: If including arbitration + class waiver, add mass arbitration procedures (batch arbitration, bellwether process)

### Class Action Waivers

Enforceable under FAA:
- *AT&T Mobility v. Concepcion* (2011): Consumer arbitration class waivers enforceable
- *Epic Systems v. Lewis* (2018): Employment arbitration class waivers enforceable
- State attempts to void class action waivers preempted by FAA

---

## M&A for Tech

### Due Diligence Framework (SaaS Acquisition)

| Category | Key Items |
|----------|-----------|
| Corporate | Certificate of incorporation, bylaws, minutes, cap table, stockholder agreements, option plan |
| IP | Patent registrations, trademark registrations, copyright registrations, domain names, IP assignments (all employees/contractors) |
| Contracts | Top 20 customer contracts, CoC provisions, key vendor contracts |
| Employment | Employment agreements, non-compete/solicitation, equity agreements, PIIAs |
| Data & Privacy | Privacy policy, GDPR compliance, DPAs, breach history |
| Litigation | Active and threatened litigation, regulatory investigations |
| Compliance | SOC 2 reports, HIPAA BAAs, PCI DSS, export controls |
| Open Source | SBOM, license compliance, AGPL/GPL exposure |
| Finance | Audited financials, ARR analysis, customer concentration, deferred revenue |
| AI-Specific | Training data licensing, model IP ownership, AI regulatory compliance |

**IP Assignment Gap Check:** Every founder, employee, contractor must have executed IP assignment. Common gaps: founders who incorporated before formal assignment; early contractors without assignment; spouse community property rights (CA, TX) on founder IP contributions.

### Rep & Warranty Standards (2024)

- General survival: 12 months (median)
- Fundamental reps (authorization, capitalization, IP ownership): 36–60 months or indefinite
- Fraud: Survives indefinitely
- Basket/deductible: ~0.5–1% of deal value
- Cap: 10–25% of deal value for general reps
- **85% of 2024 private-target deals included materiality scrape** (SRS Acquiom data)
- **R&W insurance:** Standard in deals >$50M; 2–4% premium; 1–3% retention; 10–20% coverage

### AI-Specific Reps (2024 trend)

- AI systems comply with applicable laws (EU AI Act, sector-specific rules)
- Training data was lawfully obtained (no unauthorized copyrighted works)
- No known material bias issues
- No pending AI regulatory investigations

### Earnouts

**2024 data:**
- 41% of VC-backed M&A included earnout (up from 27% in 2023)
- 33% of all private-target deals included earnout
- Median earnout = 31% of closing consideration
- Median earnout period: 24 months
- 62% use revenue as primary metric

**Structuring issues:** Post-close buyer control of business; definition of revenue (GAAP vs. bookings vs. collections); accounting methodology changes; ASC 805 fair value mark-to-market requirement.

### WARN Act

60 days notice required before mass layoffs of 50+ employees at a single site. Asset deal buyers must analyze whether post-closing workforce reductions trigger WARN Act obligations.

---

## Regulatory Compliance

### SOC 2

**Trust Service Criteria:** Security (required), Availability, Processing Integrity, Confidentiality, Privacy.

**Type I vs. Type II:**
- **Type I:** Point-in-time; controls suitably designed (cheaper, faster, less valuable)
- **Type II:** Period assessment (6–12 months); controls operating effectively—**what enterprise customers require**

**Timeline:** 3–6 months readiness + 6-month observation period + audit = 9–18 months total.

**Cost:** $30K–$150K+ for audit. Tools: Vanta, Drata, Secureframe reduce preparation cost.

**Who needs it:** Any SaaS company selling to enterprises, financial services, healthcare, or government. Enterprise RFPs virtually always require SOC 2 Type II.

### ISO 27001:2022

- 93 controls (down from 114 in 2013 edition); all 2013 certificates expired October 2025
- Stage 1 (document review) + Stage 2 (implementation) audit; annual surveillance; recertification every 3 years
- Preferred in EU and APAC; many enterprise customers require both SOC 2 + ISO 27001

### HIPAA Compliance Program

**Security Rule safeguards:**
- Administrative: Risk analysis, workforce training, access management
- Physical: Facility access, workstation security, device controls
- Technical: Access control, audit controls, integrity, transmission security (encryption)

**Annual requirements:** Risk analysis, workforce training, audit log review, BAA review, incident response testing, policy review.

### PCI DSS v4.0

Mandatory since March 31, 2025 (v3.2.1 retired). TLS 1.2 minimum (TLS 1.0/1.1 retired). Expanded MFA requirements. E-commerce script integrity requirements (Magecart attack mitigation).

---

## Key Statutes Quick Reference

| Area | Statute |
|------|---------|
| Delaware C-Corp | 8 Del. C. § 101 et seq. |
| Securities (Reg D) | 17 CFR Part 230, Rules 504–506 |
| Securities (Reg CF) | Securities Act § 4(a)(6); 17 CFR Part 227 |
| EU Privacy | (EU) 2016/679 (GDPR) |
| California Privacy | Cal. Civil Code § 1798.100 (CCPA/CPRA) |
| Children's Privacy | 15 U.S.C. § 6501; 16 CFR Part 312 (COPPA) |
| Health Data | 45 CFR Parts 160, 164 (HIPAA) |
| EU AI | (EU) 2024/1689 (EU AI Act) |
| US Trade Secrets | 18 U.S.C. § 1836 (DTSA) |
| Export Controls (Military) | 22 CFR Parts 120–130 (ITAR) |
| Export Controls (Dual-Use) | 15 CFR Parts 730–774 (EAR) |
| Equity Compensation | 26 U.S.C. §§ 83, 422 (IRC) |
| Stock Options Tax | 26 U.S.C. § 409A |
| Arbitration | 9 U.S.C. § 1 et seq. (FAA) |
| Worker Classification (CA) | Cal. Labor Code § 2775 (AB 5) |
| Mass Layoff Notice | 29 U.S.C. § 2101 (WARN Act) |

## Startup Legal Roadmap

| Stage | Priority Legal Actions |
|-------|----------------------|
| Pre-incorporation | Execute mutual NDAs; document IP origins; trademark clearance |
| Incorporation | Delaware C-Corp; founder RSPAs + 83(b); PIIAs; bylaws; initial option plan; register trademarks |
| Pre-seed | SAFE notes; cap table setup (Carta/Pulley); privacy policy + ToS if launching |
| Seed | 409A valuation; formal employment agreements; PIIAs for all contractors; GDPR/CCPA compliance |
| Series A | Board governance; D&O insurance; audited financials; IP audit; international expansion structure; formal compliance program |
| Series B+ | FedRAMP (if government); HIPAA BAA program (if healthtech); expanded privacy compliance; M&A-ready IP cleanup |
| Pre-IPO/M&A | IP gap remediation; complete compliance certifications; R&W insurance; secondary liquidity planning |
