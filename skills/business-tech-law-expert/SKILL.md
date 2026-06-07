---
name: business-tech-law-expert
description: Expert-level reference for startup founders, operators, and executives on business technology law in 2025–2026. Covers corporate formation, equity and securities law, employment law, privacy law, AI regulation, contract fundamentals, data law, export controls, antitrust, startup securities compliance, and international business. For informational/educational purposes only — not legal advice.
---

# Business Tech Law Expert

*Note: This document is for informational and educational purposes only. It does not constitute legal advice. Consult a licensed attorney for specific legal matters.*

## 1. Corporate Formation

### Delaware C-Corporation: The VC Standard

Delaware C-Corp is the virtually universal choice for VC-backed startups. Reasons:

1. **Preferred stock**: C-Corps can issue multiple classes of stock (common + multiple series of preferred). S-Corps are limited to one class. VCs require preferred stock for liquidation preferences, anti-dilution provisions, etc.

2. **QSBS eligibility**: Section 1202 QSBS exclusion (up to $15M gain tax-free per OBBBA 2025) only applies to C-Corp stock. LLCs ineligible.

3. **Employee ISOs**: Incentive Stock Options can only be issued by C-Corps to employees.

4. **Delaware Court of Chancery**: Specialized business court; predictable, sophisticated jurisprudence; no juries. 2,000+ published decisions annually on corporate law.

5. **Investor familiarity**: VCs have standard documents, legal precedent, and preferences for Delaware C-Corp structure.

**Incorporation mechanics:**
```
Step 1: File Certificate of Incorporation (Delaware Division of Corporations)
  - Authorized shares: typically 10,000,000 common + blank check preferred
  - Par value: $0.0001/share (reduces Delaware franchise tax)
  - Registered agent: required ($50–150/year)
  - Filing fee: $89

Step 2: Organizational actions (Board consent or first meeting)
  - Adopt Bylaws
  - Authorize initial issuance of founder shares
  - Elect officers (CEO, Secretary, CFO/Treasurer)
  - Approve form of stock certificates
  - Adopt equity incentive plan (usually 409A + 83(b) timing)

Step 3: Issue founder shares
  - Set vesting schedule (4-year, 1-year cliff standard)
  - All founders file 83(b) elections within 30 days
  - Engage Delaware registered agent

Typical cost: $500–2,000 DIY (Stripe Atlas, Clerky); $3,000–8,000 with law firm
```

**Delaware vs. Wyoming vs. Other States:**
| Factor | Delaware | Wyoming | Nevada |
|---|---|---|---|
| VC acceptance | Standard | Rare | Rare |
| Court of Chancery | Yes | No | No |
| QSBS support | Yes | Yes (C-Corp) | Yes (C-Corp) |
| Annual fees | ~$450 (franchise tax) | $52 | $350+ |
| Use for | VC-backed startups | LLCs, sole proprietors | Specific privacy cases |

---

## 2. Equity and Securities Law

### Regulation D: The Primary Startup Exemption

Private companies raise capital without SEC registration by using **Regulation D exemptions** under the Securities Act of 1933.

**Rule 506(b):**
- No general solicitation or advertising
- Up to 35 **non-accredited but sophisticated** investors (plus unlimited accredited)
- No SEC registration, but Form D filing required within 15 days of first sale
- Blue sky pre-empted for Rule 506 offerings
- Standard for most VC rounds

**Rule 506(c):**
- General solicitation **permitted** (public advertising)
- **Only accredited investors** (no non-accredited)
- Must take **reasonable steps** to verify accredited status
  - March 2025 SEC guidance: $200K+ investment + written rep = reasonable steps for natural persons; $1M+ for legal entities
- Less common for VC rounds; more common for public crowdfunds to accredited investors

**Accredited Investor Definitions (2023 updates in effect):**
- Natural person: $200K income (or $300K joint) in last 2 years, expected same this year
- OR net worth >$1M (excluding primary residence)
- OR Series 7, 65, or 82 license holder
- OR "knowledgeable employee" of the fund
- **OR** any person the SEC deems "sufficiently educated" (new 2023)

### Regulation CF (Equity Crowdfunding)

- Raise up to **$5M in 12 months** from non-accredited investors
- Must use an SEC-registered funding portal (Wefunder, Republic, StartEngine)
- Mandatory financial disclosure (audited if >$1.235M raised)
- Investors limited by income/net worth to $2,500–$107,000/year
- State preemption: Reg CF pre-empts state registration but not notice filings

### Regulation A+ (Mini-IPO)

- **Tier 1**: up to $20M in 12 months (state blue sky registration required)
- **Tier 2**: up to $75M in 12 months (state blue sky pre-empted, SEC qualification required)
- Non-accredited investors allowed (Tier 2 limits non-accredited to 10% of income/net worth)
- Can market publicly; shares are freely tradeable
- Annual reports (1-K) and current reports required

### Blue Sky Laws

State securities laws must be considered alongside federal exemptions.

- **Regulation D, Rule 506**: pre-empts state registration requirements (NSMIA pre-emption), but states can require **notice filings and fees** (typically $300–500 per state)
- **Key filing deadline**: most states require notice within 15 days of first sale to a resident
- **New York**: most burdensome — Martin Act gives NY AG broad anti-fraud authority independent of registration

---

## 3. Employment Law

### At-Will Employment

In 49 states (all except Montana), employers can terminate employees for any reason not prohibited by law. Key exceptions:
- **Federal anti-discrimination laws** (Title VII, ADA, ADEA, GINA, FMLA)
- **State anti-discrimination laws** (often broader than federal)
- **Public policy exceptions**: can't fire for jury duty, filing workers' comp
- **Implied contract**: if offer letter / handbook says "for cause only" — creates implied contract
- **Covenant of good faith**: California, Montana recognize this doctrine

**Wrongful termination risk:** Document performance issues before termination. Maintain neutral, objective termination paperwork.

### Title VII / ADA / ADEA

| Law | Applies To | Protected Class |
|---|---|---|
| **Title VII** | Employers with ≥15 employees | Race, color, religion, sex, national origin |
| **ADA** | Employers with ≥15 employees | Disability (reasonable accommodation required) |
| **ADEA** | Employers with ≥20 employees | Age 40+ |
| **Pregnancy Discrimination Act** | ≥15 employees | Pregnancy, childbirth |
| **GINA** | ≥15 employees | Genetic information |

**Key compliance requirements:**
- Employee handbook with anti-discrimination and harassment policies
- Annual harassment training (required in California, New York, Illinois, and others)
- EEO-1 reporting (if 100+ employees or federal contractor)
- No job postings that state age, religion, disability, or other protected characteristics

### California AB5 (Worker Classification)

California's ABC test for worker classification (Labor Code § 2750.3):
A worker is an **employee** unless the company proves ALL THREE:
- **A**: Worker is free from control and direction in performing the work
- **B**: Work is outside the usual course of the hiring entity's business
- **C**: Worker is customarily engaged in an independently established trade

**Practical impact:**
- "B" test is extremely hard to satisfy for tech companies hiring software engineers (same field)
- Most tech contractors in California should be employees
- Exceptions: licensed professionals (lawyers, architects, doctors), fine artists, content creators meeting specific criteria

**AB5 exceptions include:** over 100 specific occupations (real estate, certain entertainment, trucking [pre-empted], etc.)

**Multi-state hiring:** AB5 applies to work performed in California. Contractors working remotely from California are covered even if the company is outside California.

### Non-Competes by State (2025)

| State | Enforceability |
|---|---|
| **California** | Void and unenforceable (Business & Professions Code § 16600) |
| **North Dakota** | Void and unenforceable |
| **Minnesota** | Void (effective Jan 2023) |
| **Oklahoma** | Void except for sale of business |
| **FTC Rule (2024)** | Vacated by federal courts (2025); FTC abandoned appeal Sep 2025 |
| **Most other states** | Enforceable if reasonable in scope, geography, and duration |

**FTC status (September 2025):** The FTC's rule banning most non-competes was struck down by federal courts. FTC dropped its appeals. Non-competes are now largely a state law issue — "business as usual" at the federal level.

**FTC enforcement note:** FTC may still pursue non-competes as antitrust violations under FTC Act § 5 even without the rule, per 2025 enforcement guidelines.

**Trade secret protection as alternative to non-competes:**
- Trade secret law (DTSA) protects actual confidential information
- Narrower non-solicitation clauses (customers, employees) are enforceable in most states
- Garden leave clauses: pay employees during non-compete period (enforceable in more states)

---

## 4. Privacy Law

### GDPR Compliance for US Startups

GDPR applies to any company that processes personal data of EU residents, regardless of company location.

**Legal bases for processing:**
1. **Consent**: freely given, specific, informed, unambiguous
2. **Contract**: necessary for contract performance
3. **Legitimate interests**: business interest, balanced against individual rights
4. **Legal obligation**: compliance with EU law
5. **Vital interests**: protect someone's life
6. **Public task**: exercise of official authority

**Minimum GDPR compliance program for startups:**
```
Documentation:
  □ Privacy policy (clear, accurate, current)
  □ Records of processing activities (Article 30 ROPA)
  □ Data retention schedule
  □ DPAs with all processors (vendors handling EU personal data)

Technical measures:
  □ Encryption at rest and in transit
  □ Access controls (least privilege)
  □ Regular security assessments
  □ Breach detection and 72-hour reporting capability

Individual rights:
  □ Data subject request process (30-day response)
  □ Right to erasure ("right to be forgotten")
  □ Data portability mechanism
  □ Consent withdrawal mechanism
```

**GDPR fines:** Up to €20M or 4% of global annual turnover (whichever is higher).

### CCPA/CPRA (California)

Applies to for-profit businesses meeting one of:
- Annual gross revenues >$25M
- Buys/sells personal information of 100,000+ consumers/households annually
- Derives 50%+ of revenues from selling/sharing personal information

**Consumer rights under CCPA/CPRA:**
- Right to know what personal information is collected
- Right to delete
- Right to opt-out of sale/sharing
- Right to non-discrimination
- Right to correct (added by CPRA)
- Right to limit use of sensitive personal information (added by CPRA)

**Key difference from GDPR:** CCPA is opt-out (default data collection allowed); GDPR requires legal basis before processing.

**2025 updates:** California finalized major CCPA regulation amendments in July 2025; requirements phase in 2027–2030.

### US State Privacy Law Patchwork (2025–2026)

States with comprehensive privacy laws:
- **Active:** California, Virginia, Colorado, Connecticut, Utah, Texas, Montana, Iowa, Indiana, Tennessee, Florida, Oregon, New Hampshire, New Jersey, Delaware
- **Effective 2026:** Maryland, Kentucky, Rhode Island

**Simplification strategy:** Implement GDPR-level controls as your baseline; add California-specific controls (CPRA); you're largely covered for all US state laws.

### COPPA (Children's Online Privacy Protection Act)

- Applies to websites/apps **directed at children under 13** OR with actual knowledge of collecting data from under-13s
- Requires **verifiable parental consent** before collecting personal information
- Regulated by FTC; penalties up to $53,088 per violation per day
- **2024 COPPA 2.0 proposed rules**: would extend to teens under 16

---

## 5. AI-Specific Regulation

### EU AI Act (2024 — In Effect 2025–2026)

**Timeline:**
- February 2, 2025: Prohibited AI systems take effect
- August 2, 2025: GPAI (General Purpose AI) model obligations take effect
- August 2, 2026: High-risk AI system requirements take effect (Annex III)

**Risk tiers:**
| Risk Level | Examples | Requirements |
|---|---|---|
| **Prohibited** | Social scoring, subliminal manipulation, real-time biometric surveillance (mostly) | Banned entirely |
| **High-Risk** | Hiring AI (NYC LL 144), credit scoring, medical devices, education, law enforcement | Registration, conformity assessment, human oversight, documentation |
| **Limited Risk** | Chatbots, deepfakes | Transparency obligations (must disclose AI) |
| **Minimal Risk** | Spam filters, AI games | No requirements |

**GPAI Model Rules (August 2025):**
- All GPAI models must provide technical documentation
- Models with systemic risk (>10^25 FLOPs training) face additional requirements: adversarial testing, incident reporting, cybersecurity measures

**US companies affected:** If you sell AI systems in the EU market that qualify as high-risk, EU AI Act applies. Platform companies serving EU users must comply.

### NIST AI RMF (AI Risk Management Framework)

The US voluntary standard for AI governance. Not legally required, but:
- Referenced by FTC as evidence of reasonable AI practices
- Required by some federal contractors
- Maps to EU AI Act requirements

**Core framework:**
```
GOVERN: Establish AI governance policies, culture, accountability
MAP: Identify AI risks in context (users, use cases, potential harms)
MEASURE: Analyze and assess identified risks (metrics, testing)
MANAGE: Prioritize and treat risks (controls, monitoring, incident response)
```

### FTC AI Enforcement

FTC uses Section 5 (unfair or deceptive acts) as primary tool:
- AI products that overstate capabilities ("our AI is 100% accurate")
- Undisclosed AI processing of consumer data
- Discriminatory AI outcomes in lending, employment, housing
- Fake reviews generated by AI

### NYC Local Law 144 (Automated Employment Decision Tools)

- Effective July 2023
- Applies to employers using **automated employment decision tools (AEDTs)** for candidates or employees in NYC
- Requirements: annual bias audit by independent third party; post audit summary and results publicly; notify candidates/employees before use

**Compliance:** If your product is an AEDT used in NYC hiring, you must comply. If you use one as an employer in NYC, you must ensure your vendor has a compliant audit.

---

## 6. Contract Law Fundamentals

### Master Service Agreement (MSA) Essentials

An MSA governs the overall relationship; Statements of Work (SOWs) define specific projects.

**Key MSA provisions:**

**Limitation of Liability:**
```
Standard two-cap structure:
  Cap 1: Aggregate liability limited to fees paid in prior 12 months
  Cap 2: Uncapped categories (carve-outs): 
         - IP indemnification
         - Confidentiality breaches
         - Gross negligence / willful misconduct
         - Death/bodily injury

Vendor-favorable: only a single cap
Customer-favorable: higher cap + more uncapped carve-outs
Negotiated: 3–6 months fees cap; IP and confidentiality uncapped
```

**Indemnification:**
```
Vendor indemnifies customer for:
  - Third-party claims that the software infringes IP
  - Vendor's negligence or willful misconduct

Customer indemnifies vendor for:
  - Claims arising from customer's data
  - Customer's modifications to the software
  - Customer's use in violation of the agreement

Mutual: Data breach caused by the indemnifying party
```

**SaaS-Specific Terms:**

| Term | Vendor-Favorable | Customer-Favorable |
|---|---|---|
| **Uptime SLA** | 99.0% monthly | 99.9% monthly |
| **SLA remedy** | Service credits only | Termination right after 3 consecutive months |
| **Data retention** | 30 days post-termination | 90+ days, export-ready |
| **Security incidents** | Notice within 5 business days | Notice within 24–72 hours |
| **Price increases** | 10% annual with 30-day notice | Capped at 5%; 90-day notice |
| **Termination for convenience** | Vendor can terminate with notice | 30-day termination right for customer |

**Force Majeure (AI-era drafting):**
```
Include in covered events:
  - Natural disasters, pandemics (COVID established precedent)
  - Government actions, cyberattacks
  - Supply chain disruptions (chip shortages, GPU availability)
  - Third-party API/LLM provider outages (cloud dependency)
  
Force majeure does NOT excuse:
  - Payment obligations (always pay)
  - Data security obligations
  - Pre-existing financial difficulties
```

---

## 7. Data Law

### Data Processing Agreements (DPAs)

Required whenever a controller (your company) shares personal data with a processor (vendor who processes on your behalf).

**GDPR Article 28 mandatory DPA terms:**
- Process data only on documented instructions
- Confidentiality obligations on authorized personnel
- Security measures (Article 32 compliance)
- Sub-processor restrictions (prior written authorization)
- Data subject rights assistance
- Deletion/return of data upon contract end
- Cooperation with supervisory authorities
- Audit rights

**Standard DPA checklist:**
```
□ Clearly defined subject matter and duration of processing
□ Nature and purpose of the processing
□ Type of personal data and categories of data subjects
□ All obligations from Article 28(3) present
□ Sub-processor list attached or approval mechanism
□ Termination/return/deletion provision
□ Governing law (EU law or equivalent)
```

### Standard Contractual Clauses (SCCs)

The mechanism for lawful EU data transfers to third countries (including US):

**Current version:** EU Commission SCCs (June 2021, updated 2023)

**Four modules:**
- Module 1: Controller → Controller
- Module 2: Controller → Processor (most common for SaaS)
- Module 3: Processor → Processor
- Module 4: Processor → Controller

**UK Addendum:** Required for UK data transfers (post-Brexit; IDTA mechanism).

**Data Privacy Framework (US-EU):** US companies can self-certify to the DPF (successor to Privacy Shield) as an alternative transfer mechanism. Self-certification takes ~2–4 weeks; annual recertification required.

### Data Residency Requirements

**EU:** GDPR doesn't mandate data stays in EU, but restricts transfers to non-adequate countries without safeguards (SCCs, DPF, BCRs).

**Germany:** Some healthcare/government data requires German residency.

**China:** PIPL (Personal Information Protection Law) restricts export of personal data outside China; MLPS (Multi-Level Protection Scheme) for critical information infrastructure.

**Russia:** Federal Law 242-FZ: personal data of Russian citizens must be stored on Russian territory.

**Planning for data residency in product:**
```python
class DataResidencyRouter:
    REGIONS = {
        "EU": "eu-west-1",        # AWS Ireland
        "US": "us-east-1",         # AWS Virginia
        "UK": "eu-west-2",         # AWS London
        "APAC": "ap-southeast-1",  # AWS Singapore
        "CN": "cn-north-1",        # AWS Beijing (separate AWS account required)
    }
    
    def route_data(self, user_region: str, data_type: str) -> str:
        if user_region == "CN" and data_type == "personal":
            return self.REGIONS["CN"]  # China PIPL compliance
        elif user_region == "EU":
            return self.REGIONS["EU"]  # Default EU for GDPR
        return self.REGIONS.get(user_region, self.REGIONS["US"])
```

---

## 8. Export Controls

### EAR (Export Administration Regulations)

Administered by **BIS (Bureau of Industry and Security)**, DOC.

**What's controlled:**
- "Dual-use" items: commercial items that can also be used militarily
- Identified by Export Control Classification Number (ECCN) in Commerce Control List

**AI and software under EAR:**
- AI software and technology is classified under ECCNs 4E001, 5D002, etc.
- Mass market software (widely available) may qualify for EAR99 (no license required)
- Chips: BIS 2022/2023/2024 rules restrict export of advanced AI chips (A100, H100, and above) to China, Russia, many other countries without license

**BIS AI Diffusion Framework (January 2025):**
- Tier 1 (US allies): no chip restrictions
- Tier 2 (most countries): volumetric caps on chip exports
- Tier 3 (China, Russia, designated countries): heavy restrictions, licensing required
- Foundry Due Diligence Rule: cloud providers must verify ultimate end users of AI compute

**Practical compliance for software startups:**
```
1. Determine if your software is mass market (EAR99 likely)
2. Check whether you have sanctions screening on customer signups
   (OFAC SDN list, countries: Cuba, Iran, North Korea, Russia, Syria)
3. Do NOT deploy LLM/AI services to Tier 3 countries without BIS license
4. Cloud provider contracts should have EAR compliance clauses
```

### ITAR (International Traffic in Arms Regulations)

Administered by **DDTC (Directorate of Defense Trade Controls)**, DOS.

- Covers "defense articles and services" on the US Munitions List (USML)
- Software: if designed for military applications (targeting, navigation, weapons), likely ITAR-controlled
- Standard commercial software/AI: generally NOT ITAR (use EAR analysis instead)
- ITAR registration required if you export/broker ITAR items

**ITAR vs. EAR for AI:**
- If your AI is purely commercial (enterprise SaaS, consumer apps): EAR analysis only
- If you sell to DoD or create military-application AI: engage ITAR counsel

---

## 9. Antitrust Law

### Sherman Act Fundamentals

**Section 1 (Restraints of Trade):** Prohibits agreements that unreasonably restrain competition.
- Per se illegal: price-fixing, bid-rigging, market allocation, group boycotts
- Rule of reason: analyzed case by case (non-compete, exclusive dealing)

**Section 2 (Monopolization):** Prohibits monopolization, attempted monopolization, and conspiracy to monopolize.
- Requires: monopoly power in relevant market + willful acquisition/maintenance of that power
- Startup implications: avoid exclusionary agreements that foreclose competitors

### HSR Act (Hart-Scott-Rodino) — M&A Thresholds

Federal premerger notification required when:
- **Size of transaction**: >$111.4M (2025 threshold; adjusted annually)
- **Size of person tests** also apply for smaller transactions involving large companies

**Filing requirements:**
- Both buyer and seller file with FTC and DOJ
- Waiting period: 30 days (15 days for all-cash deals)
- Early termination (ET) possible if agencies grant
- Second request (full investigation): extends waiting period by 30 days

**For AI acquisitions:** FTC and DOJ have flagged AI-related acquisitions for enhanced scrutiny, particularly talent/tech acquisitions and GAFAM acquisitions of AI startups.

---

## 10. Startup Securities Compliance

### 409A Valuations

**Purpose:** IRC § 409A requires stock options to be granted at FMV of common stock. Incorrect valuation = immediate income recognition + 20% penalty tax on optionee.

**Safe harbor:** Independent appraisal by a "qualified appraiser" under the 409A regulations.

**When you need a 409A:**
- Before your first option grants
- After every significant event: funding round, acquisition offer, major business change
- Annually if you're issuing significant options
- Cost: $1,500–5,000/year (automated: Carta, Pulley; full appraisal: professional firm)

**Common 409A discounts to preferred price:**
- Pre-seed: 80–90% discount (common worth 10–20% of preferred)
- Series A: 50–60% discount
- Series B: 40–50% discount
- Pre-IPO: 15–25% discount

### Rule 701 (Equity to Employees)

Exemption from SEC registration for equity grants to employees, consultants, and advisors:
- Offers up to $1M in 12 months: no additional disclosure required
- Offers >$1M in 12 months: must provide financial statements and risk factors to recipients before grant
- Offers >$10M in 12 months: additional requirements; consult counsel

**Key limitation:** Only for compensatory purposes to service providers (not investors).

### Secondary Sales Restrictions

**ROFR (Right of First Refusal):**
- Company typically has ROFR in stock certificates and stockholder agreement
- Process: employee finds buyer, provides notice to company, company has 30–60 days to match
- Company often assigns ROFR to investors (investors have secondary ROFR)

**Transfer restrictions:**
- Shares typically cannot be transferred without company consent
- Exceptions: transfers to trusts, family members (with same restrictions applying)
- Public company shares are freely tradeable (no ROFR)

---

## 11. International Business

### International Entity Structures

**Common structures for US startup expansion:**

| Structure | Best For | Tax Considerations |
|---|---|---|
| **Branch office** | Testing a market, small operations | Taxed as US entity; limited liability protection |
| **Wholly-owned subsidiary** | Established market, local hiring | Local tax regime; transfer pricing rules apply |
| **Representative office** | Market research only (China) | Cannot conduct business; no revenue |
| **Joint venture** | Required markets (Saudi Arabia, some sectors) | Complex profit sharing and governance |

**Transfer pricing (arm's length principle):**
When related entities transact across borders, prices must reflect what unrelated parties would pay:
- IP licensing: royalty rate based on comparable transactions
- Services: cost-plus method or comparable uncontrolled price
- Documentation required: contemporaneous, annual TP documentation for significant intercompany transactions
- OECD BEPS project: US/EU coordination on base erosion rules

### VAT/GST for Digital Services

EU: **VAT on digital services to EU consumers**:
- B2C (consumer): charge VAT at consumer's EU country rate (15–27%)
- Use VAT One-Stop Shop (OSS) for simplified filing from one EU country
- B2B (business): reverse charge applies (customer handles VAT)
- Threshold: EU businesses owe VAT from first euro if using OSS; €10,000 if limited to 2 EU countries

**UK VAT**: Same principle post-Brexit; separate registration.

**US**: No federal VAT; state sales tax (wayfair) applies to digital goods in many states. Use TaxJar or Avalara for compliance automation.
