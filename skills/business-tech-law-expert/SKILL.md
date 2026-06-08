---
name: business-tech-law-expert
description: Expert-level business technology law advisor covering entity formation, contracts, privacy/data protection, AI regulations, cybersecurity law, employment law, consumer protection, securities law, open source compliance, and regulatory affairs for technology companies. Provides authoritative guidance on legal frameworks, compliance obligations, risk management, and strategic legal decisions for tech startups and growth-stage companies.
---

# Business Technology Law Expert

## Overview and Expert Mandate

Technology companies operate at the intersection of multiple legal disciplines simultaneously. A SaaS startup must understand contract law, privacy regulations, employment law, securities law, IP protection, and emerging AI regulations — all at once, with jurisdictional complexity spanning 50 US states and 195 countries. This skill provides expert-level legal guidance calibrated for technology companies, from incorporation through growth stage. Every domain reflects current law as of mid-2026.

**Disclaimer:** This skill provides expert legal education and frameworks. Consult qualified legal counsel for specific legal advice.

---

## 1. Entity Formation: Choosing the Right Structure

### Delaware C-Corporation: The Startup Default

For venture-backed technology companies, Delaware C-Corp is overwhelmingly standard because:
- **VC compatibility:** Most institutional investors require C-Corp structure before investing
- **Equity flexibility:** Multiple classes of stock (Common, Preferred Series A/B/C), options, warrants, convertible instruments
- **Court of Chancery:** Specialized business court with predictable, sophisticated jurisprudence
- **No physical presence required:** No need to conduct business in Delaware
- **Favorable corporate law:** Directors can be indemnified broadly; duty of care standards are well-defined

**Incorporation steps:**
1. File Certificate of Incorporation with Delaware SOS (~$89 + franchise tax)
2. Adopt bylaws (board size, officer roles, meeting procedures)
3. Issue founder shares immediately (before any IP development)
4. File 83(b) elections within 30 days of share issuance
5. Adopt Equity Incentive Plan (typically 10-20% option pool)
6. Register as foreign corporation in home state (usually California or NY)

**Franchise Tax:** Delaware taxes C-Corps via Authorized Shares Method (can be $200K+) or Assumed Par Value Capital Method (much lower for high-share-count companies). Use APVC method for startups with large authorized share counts.

### LLC Structure: When It Makes Sense

LLCs are appropriate for:
- Bootstrapped SaaS businesses with no intent to raise VC
- Single-member consulting/contractor entities
- Real estate or asset-holding entities
- Pass-through tax treatment desired by founders

LLCs cannot issue preferred stock or options in the standard VC-compatible format. Converting LLC to C-Corp before raising institutional capital triggers tax consequences and complexity.

### Public Benefit Corporation / B-Corp

PBC (Delaware) or B-Corp (certification) allows directors to consider stakeholder interests beyond shareholders. Growing adoption in mission-driven tech companies. Provides some protection for impact-focused decisions that might otherwise be challenged under Revlon duties. Not necessary unless impact mission is core to company identity and capital strategy.

### Cap Table Mechanics

Cap table = complete record of all equity ownership. Must be maintained precisely from Day 1.

**Common errors:**
- Issuing stock without board authorization
- Forgetting to file 83(b) elections
- Untracked founder equity splits leading to disputes
- Option grants without proper fair market value 409A appraisal

**Cap table structure by stage:**
```
Pre-seed:
- Founder 1:    40%
- Founder 2:    35%
- Option Pool:  15%  (pre-money, as required by lead investor)
- Advisor Pool: 10%

Post-Seed ($3M on $12M post-money):
- Founder 1:    30.0%
- Founder 2:    26.25%
- Option Pool:  11.25%
- Advisor Pool: 7.5%
- Seed Investors: 25%
```

**83(b) Election:** Must be filed with IRS within 30 days of restricted stock purchase. Elects to be taxed on grant-date FMV rather than vesting-date FMV. Critical for founders: if company grows 100x, without 83(b), you pay ordinary income tax on 100x value as shares vest. With 83(b), you pay tax on nominal value at grant. Missing this window is irreversible.

**Tools:** Carta (dominant cap table management platform), Pulley, Capshare. Use software from Day 1 — spreadsheet cap tables become unmaintainable.

---

## 2. Board Structure and Governance

### Early-Stage Board Composition

- **Seed stage:** 3 directors — 2 founders, 1 independent (or lead investor)
- **Series A:** 5 directors — 2 founders, 2 investors (lead + one more), 1 independent
- **Series B+:** 7-9 directors — balance shifts toward more independent members

### Fiduciary Duties

Directors owe duties to the corporation (not just shareholders):
- **Duty of Care:** Make decisions on informed basis; review materials before meetings
- **Duty of Loyalty:** No self-dealing; disclose and recuse on conflicted transactions
- **Duty of Candor:** Disclose all material information in M&A situations

**Business Judgment Rule:** Courts defer to board decisions made in good faith, with reasonable information, and without conflict of interest. Maintain board minutes as evidence of process.

### D&O Insurance

Directors & Officers insurance is non-negotiable by Series A. Covers legal defense costs and damages from shareholder lawsuits. Standard coverage at Series A: $5-10M. At IPO: $50-100M. Side A coverage protects individual directors when company cannot indemnify.

### Written Consents vs. Meetings

Delaware law allows all corporate actions via written consent (no meeting required). Critical for speed at early stages. All consents must be signed by required percentage of shares (varies by action type). Maintain consent minute book.

---

## 3. Contracts: The SaaS Legal Stack

### Master Service Agreement (MSA)

The MSA is the foundational enterprise contract. Key provisions:

**Limitation of Liability (LoL):**
```
Standard: Each party's liability capped at fees paid/payable in prior 12 months
Carve-outs from LoL (mutual): Confidentiality breaches, IP indemnification, gross negligence/willful misconduct
Carve-out from LoL (customer): Payment obligations
```

**Indemnification Matrix:**
| Party | Indemnifies For |
|-------|----------------|
| Vendor | IP infringement claims against customer's use of product |
| Customer | Claims arising from customer data, customer misuse |
| Both | Own negligence and willful misconduct |

**IP Ownership:** Vendor retains all IP in the software. Customer owns their data. Customer grants vendor license to use data to provide services (and potentially aggregate/anonymize for product improvement — negotiate scope carefully).

**Warranty Disclaimers:** Disclaim implied warranties of merchantability, fitness for particular purpose. Include uptime warranty (99.9% = 8.7 hours/year downtime) referenced in SLA.

### Order Form Architecture

Order Forms execute under MSA and specify:
- Product name and description
- Subscription term (1-year minimum for SaaS)
- Seat count / API call limits / usage tier
- Fees and payment terms (Net-30 standard)
- Auto-renewal provisions (critical for ARR; require 30-60 day notice to cancel)
- Effective date

### Statement of Work (SOW)

For professional services attached to SaaS:
- Deliverables with acceptance criteria (not vague)
- Payment milestones
- Change order process
- Intellectual property: services IP should vest in customer; platform IP retained by vendor

### SLA (Service Level Agreement)

Define:
- **Uptime commitment:** 99.9% (three nines) = 8.7 hrs/yr; 99.95% = 4.4 hrs/yr; 99.99% = 52 min/yr
- **Exclusions:** Scheduled maintenance, force majeure, customer actions
- **Measurement:** Third-party monitoring tool preferred (Datadog, Pingdom)
- **Remedies:** Service credits (10-30% of monthly fee per incident); NOT refunds; NOT consequential damages
- **Support tiers:** P1 (system down) 1-hour response; P2 (degraded) 4-hour; P3 (minor) 1 business day

### NDA Best Practices

- Use mutual NDAs for early-stage discussions
- Limit confidentiality obligation to 3-5 years (perpetual NDAs are unenforceable in some states)
- Carve-outs: publicly known info, independently developed, received from third parties without restriction, required by law
- Residuals clause: standard in enterprise — allows employees to retain general knowledge in unaided memory
- Trade secret protection: Label all sensitive materials "Confidential" — unlabeled materials may lose protection

---

## 4. Privacy and Data Protection Law

### GDPR (2025 Status)

The EU General Data Protection Regulation remains the global standard-setter. Key 2025 updates:
- **EU-US Data Privacy Framework (DPF):** Validated and operational as of September 3, 2025. Provides legal transfer mechanism for US-EU data flows. Must self-certify via DPF program; CISA administers.
- **Standard Contractual Clauses (SCCs):** Module 2 (controller-to-processor) required in all vendor DPAs
- **GDPR enforcement:** Fines up to 4% of global annual turnover or €20M

**Core GDPR compliance program:**
1. **Data mapping:** Document all personal data flows (what, why, where, how long, who)
2. **Legal basis:** Document lawful basis for each processing activity (consent, contract, legitimate interest, legal obligation)
3. **Privacy notices:** Provide transparent, layered notices at collection
4. **DPAs:** Execute Data Processing Agreements with all processors; flow-down obligations to sub-processors
5. **Individual rights:** Build systems to respond to access, deletion, portability, restriction requests within 30 days
6. **Data breach notification:** 72-hour notification to supervisory authority; notify affected individuals for high-risk breaches
7. **DPO appointment:** Required for large-scale processing of special categories; optional but recommended for most tech companies
8. **Article 30 Records of Processing:** Maintain records of all processing activities

### CCPA/CPRA (California)

California Consumer Privacy Act (amended by CPRA effective January 1, 2023):
- **Threshold:** Revenue >$25M, OR process data of 100,000+ CA consumers/households, OR derive >50% revenue from selling/sharing personal information
- **Rights:** Know, delete, correct, opt-out of sale/sharing, limit sensitive PI use, non-discrimination
- **Enforcement:** California Privacy Protection Agency (CPPA); $2,500/unintentional violation, $7,500/intentional
- **New in CPRA:** Right to correct; limit use of sensitive PI; expanded employee/B2B data coverage

### State Privacy Law Landscape (2025-2026)

19+ states have comprehensive privacy laws enacted as of mid-2026:

| State | Law | Effective | Key Distinction |
|-------|-----|-----------|-----------------|
| California | CCPA/CPRA | Jan 2020/Jan 2023 | Revenue/volume trigger |
| Virginia | VCDPA | Jan 2023 | Controller/processor framework |
| Colorado | CPA | Jul 2023 | Universal opt-out required |
| Connecticut | CTDPA | Jul 2023 | Profiling protections |
| Texas | TDPSA | Jul 2024 | **No revenue minimum** — applies to any business processing TX residents' data |
| Florida | FDBR | Jul 2024 | $1B revenue threshold (enterprise only) |
| Nevada | SB 370 | Oct 2024 | Opt-out of data broker sales |
| Montana | MCDPA | Oct 2024 | Small business carve-outs |
| Oregon | OCPA | Jul 2024 | Broad sensitive data categories |
| New Hampshire | NHPA | Jan 2025 | Modeled on Virginia VCDPA |

**Texas TDPSA critical note:** No revenue minimum — any company processing personal data of Texas residents is potentially covered. This is the broadest jurisdictional trigger of any state law.

### Consent Management Platform (CMP)

For consumer-facing products:
- Implement cookie banner/consent management with IAB TCF 2.2 compliance
- Distinguish between necessary cookies (no consent required) and analytics/marketing (consent required)
- Honor Global Privacy Control (GPC) signal for California and Colorado users
- Document consent records with timestamps

### AI and Sensitive Data

Special considerations for AI-powered products:
- Sensitive categories (health, race, religion, biometrics, precise location, sexual orientation) require explicit consent or legal justification
- Automated decision-making with "significant effects" requires human review rights under GDPR Article 22
- Profiling using inferred characteristics may trigger additional obligations
- Privacy by design: minimize data collection; anonymize/pseudonymize where possible

---

## 5. AI Regulations

### EU AI Act (In Force 2025-2026)

The world's first comprehensive AI law. Timeline:
- **February 2, 2025:** Chapter I (definitions), Chapter II (prohibited practices) in force
- **August 2, 2025:** General-Purpose AI (GPAI) provisions (Title VIII) in force; governance rules apply
- **August 2, 2026:** High-risk AI system requirements applicable

**Prohibited Practices (in force Feb 2025):**
- Social scoring by public authorities
- Real-time biometric identification in public spaces (with narrow exceptions)
- Subliminal manipulation techniques
- Exploiting vulnerabilities of specific groups
- Emotion recognition in workplace/education (with exceptions)
- AI systems creating facial recognition databases from internet scraping

**GPAI (General Purpose AI) Requirements (in force Aug 2025):**
- All GPAI models must maintain technical documentation
- Copyright transparency: training data summaries
- High-impact GPAI (≥10^25 FLOPs training compute): cybersecurity testing, incident reporting, red-team testing

**High-Risk AI (August 2026):**
High-risk categories include: biometric identification, critical infrastructure management, education, employment, essential services, law enforcement, migration, administration of justice.

Requirements for high-risk AI:
- Risk management system
- High-quality training data
- Technical documentation
- Record-keeping and logging
- Transparency for users
- Human oversight measures
- Accuracy, robustness, cybersecurity

**Penalties:** Up to €35M or 7% of global turnover for prohibited practices; €15M or 3% for other violations.

### US AI Regulation (2025)

145 state AI laws enacted in 2025 — most fragmented regulatory environment in history.

**Key state laws:**
- **Colorado SB 205:** Effective June 30, 2026. "Consequential decisions" AI systems (employment, credit, education, health, housing, legal services) must conduct impact assessments, disclose AI use to consumers, allow opt-out, provide human review of adverse decisions
- **New York City LL 144:** Automated employment decision tool (AEDT) bias audits required; disclosure to job applicants; annual third-party auditing. In force
- **California AB 2013 (2025):** AI training data transparency for systems used in "high-stakes decisions"
- **Illinois AEIA:** AI-generated employment interview analysis disclosure

**Federal AI Landscape:**
- No comprehensive federal AI law as of mid-2026
- FTC actively pursuing "AI washing" enforcement under existing Section 5 UDAP authority
- SEC guidance on AI disclosures for public companies
- NIST AI RMF (Risk Management Framework) 1.0: voluntary framework increasingly referenced in contracts and regulatory guidance
- Executive Orders on AI: current administration has modified prior EO 14110; focus on deregulation and innovation

### AI Washing Enforcement

The FTC has made AI misrepresentation a priority:
- **DoNotPay:** $193,000 settlement (January 2025) for misrepresenting AI legal services capabilities
- **Cleo AI:** $17 million settlement (March 2025) for misleading AI-powered financial advice claims
- **Rule:** Any claim that an AI system can do something must be accurate, substantiated, and not misleading. "AI-powered," "intelligent," "autonomous" — all subject to scrutiny.

---

## 6. Cybersecurity Law

### SEC Cybersecurity Rules (Public Companies)

Form 8-K Item 1.05: Material cybersecurity incidents must be disclosed within **4 business days** of determining materiality. Materiality = reasonable investor would consider information important.

Form 10-K/20-F: Annual disclosure of:
- Cybersecurity risk management processes
- Board oversight of cybersecurity risks
- Management's role in assessing cybersecurity risks

**For private companies:** SEC rules don't apply directly, but increasingly required by enterprise customers in vendor agreements.

### CIRCIA (Cyber Incident Reporting for Critical Infrastructure Act)

Signed 2022; CISA rulemaking ongoing. Final rules expected 2025-2026. When in effect:
- **Covered entities** (critical infrastructure sectors): Report "covered cyber incidents" within 72 hours; ransom payments within 24 hours
- Significant data breach, unauthorized access to systems, disruption of operations qualify
- CISA can subpoena reporting entities for additional information

### HIPAA (Health Data)

Applies to Covered Entities (healthcare providers, health plans, clearinghouses) and Business Associates (vendors processing PHI on their behalf).

**Technical Safeguards required:**
- Access controls (unique user IDs, automatic logoff, encryption)
- Audit controls (hardware/software activity logging)
- Integrity controls (PHI not improperly altered)
- Transmission security (encryption in transit)

**Breach Notification:**
- Notify HHS within 60 days of discovery (or 60 days year-end for smaller breaches)
- Notify affected individuals promptly
- Breaches of 500+ individuals in a state: notify prominent media

**HIPAA penalties 2025:** $137 to $2,067,813 per violation category per year

If your SaaS product touches any healthcare data, assume HIPAA applies and build accordingly. Sign Business Associate Agreements (BAAs) with all vendors processing PHI.

### SOC 2 Type II

Not law, but contractual requirement for most enterprise sales. AICPA Trust Services Criteria:
- **Security** (required): CC6 Logical and Physical Access Controls
- **Availability:** System available for operation as committed
- **Processing Integrity:** Processing complete, valid, accurate, timely
- **Confidentiality:** Information designated confidential is protected
- **Privacy:** Personal information collected, used, retained, disclosed per criteria

Type I = point-in-time; Type II = 6-12 month observation period (required for enterprise). Target SOC 2 Type II before Series B.

---

## 7. Employment Law

### Classification: Employees vs. Contractors

**ABC Test** (California, Massachusetts, New Jersey, others):
Worker is an independent contractor only if ALL three are true:
- (A) Free from company's control and direction
- (B) Performs work outside usual course of company's business
- (C) Customarily engaged in independently established trade/occupation

Misclassification risk: Back wages, benefits, payroll taxes, penalties. California: Private right of action under PAGA, with 25% to worker and 75% to state.

**Economic Reality Test** (federal, FLSA): Multi-factor test including permanency, integral to business, investment in facilities, opportunity for profit/loss, control.

**Safe contractor indicators:** Own tools/equipment, multiple clients, sets own hours, invoices separately, has own business entity, makes independent business decisions.

### Non-Compete Agreements

**FTC Non-Compete Ban:** The FTC's April 2024 rule banning most non-competes was struck down in federal court (Fifth Circuit, August 2024; cert denied September 2025). The ban is **dead** as of September 2025.

**State-by-state landscape:**
- **California:** Non-competes unenforceable; California residents protected even if agreement specifies another state's law (AB 1076 2024)
- **Minnesota:** Non-competes banned (2023)
- **North Dakota, Oklahoma:** Unenforceable
- **Most other states:** Enforceable if reasonable in duration (6-24 months), geographic scope, and activity

**Best practice:** Use trade secret protection (NDAs + proper procedures) rather than broad non-competes. Non-solicitation of customers and employees is generally more enforceable.

### Immigration

**H-1B Visa (Specialty Occupation):**
- Annual cap: 65,000 regular + 20,000 US master's exemption
- Lottery: Filed in March for October 1 start
- 2025 update: Minimum salary threshold increased; specialty occupation definition tightened; ~$100K minimum for most tech roles
- Employer must pay "prevailing wage" (Level I-IV per DOL Foreign Labor Certification)

**O-1A (Extraordinary Ability):**
- No cap, no lottery
- Evidence of sustained national/international acclaim
- 8 criteria (must meet 3+): Awards, membership in associations requiring outstanding achievement, press, judging others' work, original contributions, scholarly articles, critical employment, high salary
- Strong option for exceptional engineers and founders unable to win H-1B lottery

**PERM Labor Certification:** Required for EB-2/EB-3 green cards. Employer proves no qualified US workers available. 8-18 month DOL processing. Country backlogs: India EB-2 currently 10+ years.

### Equity Compensation

**Stock Options:**
- **ISO (Incentive Stock Option):** Employee-only; favorable tax treatment (no ordinary income at exercise; capital gains if held); AMT risk on spread; $100K annual ISO limit; must be exercised within 90 days of termination (or option lapses)
- **NSO (Non-Qualified Stock Option):** Employees and non-employees; ordinary income on spread at exercise; company gets deduction

**RSUs (Restricted Stock Units):**
- Increasingly common at late-stage/pre-IPO companies
- Taxed as ordinary income on vesting (two-condition RSUs: service + liquidity)
- Simpler than options for employees who don't want to write check to exercise

**409A Valuation:**
- Required for all option grants; sets "fair market value" for strike price
- Must be current (within 12 months, or more recent if material event)
- Safe harbor: Third-party independent appraisal rebuttable presumption
- Failure: All options become immediately taxable as compensation + 20% penalty + interest

**Vesting Standards:**
- Standard: 4-year vesting, 1-year cliff
- Accelerated vesting: Single-trigger (change of control) vs double-trigger (CIC + termination)
- Board approval required for all grants

---

## 8. Consumer Protection Law

### FTC Act Section 5 (Unfair or Deceptive Acts or Practices)

The FTC's broad mandate to prohibit "unfair or deceptive acts or practices" covers:
- False advertising
- Misleading AI capability claims
- Dark patterns
- Hidden fees
- Data privacy violations

**Negative Option Rule (Mid-2025):** FTC amended the Negative Option Rule to require:
- Clear and conspicuous disclosure of subscription terms before enrollment
- Simple cancellation mechanisms (as easy as sign-up)
- Annual reminders for continuity programs
- Prohibition on cancellation "save" flows that deceive consumers

Enforcement precedent: FTC v. Amazon (2023) — illegal cancellation flows; $25M settlement.

### Terms of Service / End-User License Agreement

**Clickwrap vs. Browsewrap:**
- **Clickwrap:** User clicks "I agree" button or checkbox. Courts consistently enforce. Requires affirmative action.
- **Browsewrap:** Notice at bottom of page ("By using this site, you agree to..."). Frequently unenforceable — courts require actual or constructive notice.
- **Best practice:** Clickwrap with prominent notice and specific acknowledgment; present full ToS before acceptance; store timestamp + user record of acceptance

**Key ToS/EULA provisions:**
- Arbitration clause (class action waiver): Generally enforceable in B2C; varies by state. SCOTUS has upheld class action waivers consistently.
- Limitation of liability: Exclude consequential/indirect damages; cap at amounts paid
- Governing law/venue: Delaware for B2B; careful with California (CCPA applies regardless)
- Modification of terms: Right to modify with notice; continued use = acceptance
- Acceptable use policy: Prohibit illegal use, reverse engineering, scraping
- Termination for breach: Reserve right to terminate accounts for ToS violations

### Dark Patterns

FTC and state AGs increasingly targeting dark patterns:
- Pre-checked boxes for add-ons
- Hidden fees revealed only at checkout
- Confirm-shaming cancel buttons ("No thanks, I don't want to save money")
- Roach motel subscriptions (easy to join, difficult to cancel)
- Disguised ads
- Forced disclosure (blocking access until consent given)

---

## 9. Securities Law

### Private Company Fundraising Exemptions

**Regulation D, Rule 506(b):**
- No SEC registration required
- Up to 35 non-accredited investors (with disclosure requirements) + unlimited accredited investors
- No general solicitation permitted
- File Form D within 15 days of first sale
- Most common for VC rounds

**Regulation D, Rule 506(c):**
- General solicitation permitted (social media, conferences, etc.)
- ALL investors must be accredited + verified (reasonable steps)
- Less common due to verification burden

**Regulation CF (Crowdfunding):**
- $5 million annual cap (raised from $1.07M in 2021)
- Open to non-accredited investors
- Requires intermediary (funding portal or broker-dealer)
- Ongoing disclosure requirements (annual report)
- 12-month holding period for securities sold

**Accredited Investor Definition (post-2020):**
- Income: $200K individual / $300K joint for last 2 years + expectation of same
- Net worth: $1M+ excluding primary residence
- Professional: FINRA Series 7, 65, 82 licenses; knowledgeable employees of private funds; "family offices" managing $5M+

### SAFEs and Convertible Notes

**Post-Money SAFE (YC Standard 2018+):**
- No interest, no maturity date
- Converts at next priced round at discount (typically 15-20%) or valuation cap
- **Post-money** cap means dilution is clear at signing
- Valuation cap vs. discount: Investor gets better of the two at conversion
- MFN (Most Favored Nation) clause: if subsequent SAFEs have better terms, this SAFE automatically gets those terms (pre-money SAFEs only)
- 90%+ of pre-seed rounds use Post-Money SAFEs as of 2025

**Convertible Notes:**
- Interest-bearing debt (typically 5-8%)
- Maturity date (12-24 months) — creates pressure to raise priced round
- Discount (15-20%) and/or valuation cap
- Triggers: Qualified financing (typically >$1M), change of control, maturity
- Interest converts along with principal at next round

### State Securities Laws (Blue Sky)

Despite federal preemption for most VC deals, must file:
- **Form D** with SEC within 15 days
- **State notice filings** in each state where investors reside (most states require this for 506(b)/(c) deals)
- Fees: $0-$2,500 per state depending on jurisdiction

### Token/Crypto Securities Analysis

If your startup issues tokens, apply the **Howey Test:**
- Investment of money
- In a common enterprise
- With expectation of profits
- Derived from efforts of others

If all four prongs satisfied, token is likely a security. SEC has brought enforcement actions against numerous token issuers. Path to compliance: Reg D exempt offering + lockup periods, or Reg A+ qualification.

---

## 10. Open Source Compliance

### License Categories

**Permissive Licenses (business-friendly):**
- MIT: Use freely, retain copyright notice
- Apache 2.0: Use freely, retain notices, patent grant, must disclose modifications
- BSD 2-Clause/3-Clause: Similar to MIT with additional clauses

**Copyleft Licenses (viral — require careful management):**
- **GPL v2/v3:** If you distribute software that incorporates GPL code, your software must be licensed under GPL. "Distribution" triggers = shipping binary or source to third parties.
- **LGPL:** Lesser GPL; allows linking without viral effect; still restricts modifications to the library itself
- **AGPL (Affero GPL):** **SaaS problem.** AGPL treats "network use" as distribution. If you run AGPL software on your server and let users access it via network, you must make your entire application's source code available under AGPL. Critical risk for SaaS companies.

**AGPL SaaS Risk:**
Many popular libraries have AGPL versions (MongoDB had SSPL; Ghostscript; some AI frameworks). Before using AGPL-licensed software in your SaaS:
1. Obtain a commercial license from the copyright holder, OR
2. Rewrite the functionality, OR
3. Accept that your source code must be made available

### Software Composition Analysis (SCA)

Implement SCA tools in CI/CD pipeline:
- **Snyk:** Commercial SCA + security scanning
- **FOSSA:** License compliance automation
- **Black Duck:** Enterprise-grade SCA
- **OSS Review Toolkit (ORT):** Open source option

Generate Software Bill of Materials (SBOM) in SPDX or CycloneDX format — required by:
- US Executive Order 14028 (federal software procurement)
- FDA (medical device software, 2023)
- EU Cyber Resilience Act (proposed)
- Major enterprise customers increasingly requiring in procurement

### Inbound vs. Outbound License Strategy

**Inbound:** Licenses under which you receive third-party code (must comply with each)
**Outbound:** License under which you distribute your own software

For proprietary SaaS: Outbound is "All Rights Reserved" / proprietary — no obligation to distribute source. But inbound licenses (especially AGPL) can override this.

**Contributor License Agreement (CLA):** If accepting external contributions to your codebase, require CLA to ensure you can relicense contributions. Use Apache Software Foundation CLA as template.

---

## 11. AI Regulatory Affairs: Building the Compliance Program

### EU AI Act Compliance Program (for companies with EU exposure)

**Step 1: AI System Inventory**
- Catalog all AI systems used in products/operations
- Classify: Prohibited / High-risk / Limited risk / Minimal risk
- Document intended purpose, affected users, deployment context

**Step 2: Conformity Assessment**
For high-risk AI systems:
- Internal assessment (most systems): Document compliance with Annex IV requirements
- Third-party assessment: Required for biometric ID, critical infrastructure, some employment systems

**Step 3: Technical Documentation**
Minimum per Annex IV:
- System description and intended purpose
- Architecture and model description
- Training/validation data description
- Accuracy, robustness, cybersecurity measures
- Risk management measures
- Human oversight capabilities

**Step 4: Incident Reporting**
Register in EU database of high-risk AI systems. Report serious incidents to national authority within timeframes per Article 73.

### NIST AI Risk Management Framework

NIST AI RMF 1.0 (January 2023) — voluntary but increasingly industry-standard:
- **Govern:** AI risk culture, policies, accountability
- **Map:** AI context, risks, impacts
- **Measure:** Analyze, assess, track risks
- **Manage:** Allocate risk responses, plan for risk

Implement AI RMF 1.0 as internal governance framework even if not legally required — demonstrates responsible AI practices to regulators, enterprise customers, and investors.

### AI Bias and Fairness

**NYC LL 144:** Automated employment decision tools must:
- Undergo annual independent bias audits
- Post audit summary on website
- Notify candidates that AEDT is being used
- Provide alternative assessment on request

**Colorado SB 205 (effective June 30, 2026):** High-risk AI in consequential decisions:
- Impact assessment before deployment
- Consumer disclosure of AI use
- Consumer right to opt-out
- Human review of adverse decisions
- Vendor certification that AI system complies

---

## 12. Intellectual Property Strategy

### Patent Strategy

**Types:**
- **Utility Patents:** Protect functional innovations; 20-year term; 12-month filing window from public disclosure
- **Provisional Applications:** 12-month placeholder; establishes priority date; cannot be enforced
- **Design Patents:** Protect ornamental appearance; 15-year term; strong for UI/product design

**Software Patents:** Patent-eligible under Alice/Mayo analysis if claim is directed to specific technological improvement (not abstract idea alone). Work with patent counsel to draft claims that survive Alice challenges.

**Patent Prosecution Timeline:**
- File provisional: Day 0
- File non-provisional (PCT or US): Month 12
- Office actions: 18-36 months post-filing
- Patent issued: 2-4 years typically

**Cost:** $15-25K per patent (prosecution + attorney fees). Patent portfolio at Series B: 3-10 patents minimum in core technology area.

### Trade Secret Protection

Trade secrets protected under Defend Trade Secrets Act (DTSA) and Uniform Trade Secrets Act. Requirements:
- Information derives economic value from not being generally known
- Reasonable measures to maintain secrecy

**Reasonable measures:**
- NDA with all employees, contractors, partners
- Access controls (need-to-know basis)
- Physical security measures
- Exit interview procedures
- Marking documents as "Confidential"
- Technical access logging

Trade secret misappropriation remedies: Injunctive relief, actual damages, punitive damages (2x actual), attorney's fees.

### Trademark Strategy

- Register core brand name and logo with USPTO (filing fee: $250-350/class)
- Class 42 (Software as a Service) — most tech companies
- Consider international registration via Madrid Protocol for key markets
- Monitor for infringers using TMHUNT or similar services
- Use ™ immediately; ® only after federal registration

### Copyright in AI-Generated Content

US Copyright Office guidance (2023-2024): AI-generated content without human creative authorship is not copyrightable. Human-AI collaborative works: only the human-authored portions are protected.

**Implications:**
- If you use AI to generate marketing copy, code, or design, document human creative input
- Training data copyright: Fair use defense for AI training contested; ongoing litigation (Getty v. Stability AI, NYT v. OpenAI)
- Output ownership: Review terms of AI service providers regarding ownership of generated content

---

## 13. Legal Risk Management Framework

### Legal Budget by Stage

| Stage | Annual Legal Budget | Priorities |
|-------|---------------------|------------|
| Pre-seed | $10-25K | Incorporation, founder agreements, IP assignment |
| Seed | $25-75K | First fundraise, employment agreements, privacy policy |
| Series A | $75-200K | Financing docs, MSA template, option plan |
| Series B | $200-500K | M&A readiness, international expansion, compliance |
| Series C+ | $500K-2M+ | Securities compliance, IPO prep, litigation defense |

### Legal Technology Stack

- **Contract Management:** Ironclad, ContractWorks, Conga
- **Cap Table:** Carta (dominant), Pulley, Capshare
- **E-Signature:** DocuSign (enterprise), HelloSign (startup)
- **Privacy Compliance:** OneTrust, TrustArc, Osano
- **IP Management:** Anaqua, IP Folio
- **Entity Management:** Carta Entity, Clerky (early stage)

### When to Hire General Counsel

- **Seed stage:** Fractional GC (3-10 hours/week) or law firm on retainer
- **Series A:** Consider VP Legal (not GC) to manage outside counsel
- **Series B / $5-10M ARR:** First GC hire (typically 8-15 years experience)
- **Pre-IPO:** Build full legal team (GC + employment counsel + securities counsel + privacy counsel)

### Litigation Risk Management

- **Document retention policy:** Preserve records per legal hold requirements
- **Litigation hold:** Immediately suspend document destruction upon litigation notice
- **E-discovery readiness:** Use legal hold management tools (ZL Technologies, Relativity)
- **Attorney-client privilege:** Mark privileged communications; outside counsel for sensitive issues
- **Insurance:** General Liability, E&O (Professional Liability), Cyber Liability, D&O (pre-Series A: $5K/year; Series A: $25-50K/year)

---

## Quick Reference: Legal Decision Matrix

| Situation | Action Required | Urgency |
|-----------|----------------|---------|
| Founding company | Incorporate Delaware C-Corp; issue shares; file 83(b) | Immediate |
| First employee | Offer letter + PIIA + background check | Before Day 1 |
| First contractor | IC agreement + IP assignment | Before engagement |
| Raising pre-seed | Post-Money SAFE; Reg D 506(b) Form D | Within 15 days of first close |
| Using AI tools | Review AI vendor ToS for IP ownership and data use | Before deployment |
| Handling EU data | Execute DPAs with all processors; privacy notice | Before processing |
| Enterprise customer | MSA negotiation; DPA; SOC 2 roadmap | Before contract signing |
| Employee departing | Exit interview; confirm NDA; revoke access | Day of departure |
| Data breach | Assess materiality; notify within applicable windows | Within 24-72 hours |
| AGPL dependency detected | Legal review; commercial license or replacement | Before production |
