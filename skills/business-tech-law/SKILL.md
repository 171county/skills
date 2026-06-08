---
name: business-tech-law
description: Expert-level skill covering business and technology law for startups and growing tech companies. Covers entity formation, SaaS contracting, employment and contractor classification, AI regulation compliance, data privacy law, equity mechanics, and M&A due diligence. Use this when analyzing legal risk, structuring deals, drafting contracts, evaluating regulatory exposure, or advising on compliance strategy.
---

# Business Tech Law Expert

> **Disclaimer:** This skill provides educational legal information, not legal advice. Engage qualified legal counsel for specific legal matters.

---

## Entity Formation: Why Delaware C-Corp

The vast majority of VC-backed tech startups incorporate as **Delaware C-Corporations**. This is not tradition — it is the optimal structure for the venture-backed path.

### Why Delaware

**Predictable corporate law:** Delaware General Corporation Law (DGCL) is the most developed in the US. Thousands of court decisions, including the Court of Chancery (specialized business court with no jury), mean outcomes are predictable.

**Investor familiarity:** Every institutional VC uses Delaware-standard documents (NVCA templates, Y Combinator SAFEs). Non-Delaware structures create friction and legal fees.

**The business judgment rule:** Delaware courts give directors maximum deference on business decisions, protecting founders and boards from shareholder suits for ordinary business decisions.

**Tax planning:** C-Corps enable QSBS (Qualified Small Business Stock, §1202) exclusion — up to $10M (or 10x basis) in gain excluded from federal capital gains tax if held 5+ years and the company qualifies.

### C-Corp vs. LLC vs. S-Corp

| Factor | C-Corp | LLC | S-Corp |
|---|---|---|---|
| VC investment | Standard — no friction | VCs typically won't invest | VCs won't invest |
| QSBS eligible | Yes (§1202) | No | No |
| Pass-through tax | No (double taxation) | Yes | Yes |
| Equity compensation | Standard options (ISOs/NSOs) | Complex units | Restricted |
| Foreign investors | No restrictions | Restricted | Prohibited |

**When an LLC makes sense:** Single-founder, bootstrapped, no equity grants planned, wants pass-through taxation. Still: convert to C-Corp before raising institutional capital.

### State of Incorporation vs. Headquarters

- **Incorporate in Delaware**, regardless of where you operate
- **Register as foreign corporation** in your operating state (typically California, New York, etc.)
- **Registered agent in Delaware**: ~$50–$300/year. Required.

---

## SaaS Commercial Contracts

### Master Subscription Agreement (MSA)

The governing contract for SaaS deals. Key provisions:

**Acceptable Use Policy (AUP):** What the customer can and cannot do with the software. For AI products, explicitly prohibit: using outputs to train competing models, using to generate illegal content, circumventing safety measures.

**Data Processing Agreement (DPA):** Required for GDPR compliance whenever you process EU personal data on behalf of customers. Standard Contractual Clauses (SCCs) needed for transfers outside EU.

**Service Level Agreement (SLA):**
- Uptime commitment: 99.9% = ~8.7 hours/year downtime; 99.99% = ~52 min/year
- Measurement: exclude scheduled maintenance, force majeure, customer-caused outages
- Remedies: service credits (not cash) — typically 10–25% of monthly fee for each % below SLA
- Avoid promising SLA without ops infrastructure to back it

**Limitation of Liability (LOL) Clause:**
```
IN NO EVENT WILL EITHER PARTY'S AGGREGATE LIABILITY ARISING OUT OF OR RELATED 
TO THIS AGREEMENT EXCEED THE GREATER OF (A) $50,000 OR (B) THE AMOUNTS PAID 
OR PAYABLE BY CUSTOMER TO VENDOR IN THE TWELVE (12) MONTHS PRECEDING THE CLAIM.
```
Standard: cap at 12 months of fees. Carve out mutual indemnification for IP infringement, confidentiality breach, and gross negligence.

**Mutual Indemnification:** Vendor indemnifies customer for IP infringement claims against the software. Customer indemnifies vendor for claims arising from customer's use violating AUP. Increasingly, AI vendors add carve-outs for infringement claims arising from customer-provided training data.

### Enterprise SaaS Negotiation Points

| Clause | Customer Pushes For | Vendor Default |
|---|---|---|
| Data ownership | Customer owns all data, unlimited export | Vendor owns aggregated/anonymized data |
| SLA uptime | 99.99% with cash refunds | 99.9% with service credits |
| LOL cap | 2x annual fees | 12 months fees |
| IP indemnification | Broad, including AI outputs | Software only, excluding outputs |
| Audit rights | Annual audit, source code escrow | None |
| AI training | Prohibition on training on customer data | Right to train on anonymized data |

### Order Forms and SOWs

Order forms execute against the MSA. Key fields:
- Subscription term (typically 1 year, auto-renews)
- Seats/usage tier
- Fees and payment terms (Net 30 standard; enterprise may request Net 60)
- Renewal pricing cap (customer wants ≤CPI+3%; vendor wants unlimited)

---

## Employment and Contractor Classification

### Employee vs. Independent Contractor

The stakes: misclassification = back taxes, penalties, and potential class action (Dynamex/AB5 in California; $10K–$25K per worker penalties).

### California AB5 (Effective January 2020)

California uses the **ABC test**. A worker is an employee unless ALL THREE prongs are met:

**A.** The worker is free from the hiring entity's control and direction in performing the work, both under the contract and in fact;

**B.** The worker performs work outside the usual course of the hiring entity's business; and

**C.** The worker is customarily engaged in an independently established trade, occupation, or business of the same nature as the work performed.

**Prong B is the killer for tech startups:** An engineer or AI researcher performing work "in the usual course of [your] business" (building software, training models) cannot be an independent contractor under AB5.

**Exemptions:** Doctors, lawyers, licensed engineers (B2B contracts only), some creative professionals, and others have industry-specific exemptions.

**Federal standard (IRS):** The 20-factor "right to control" test — more flexible than ABC test, but AB5 applies in California regardless of federal classification.

### Avoiding Misclassification

**Truly independent contractor hallmarks:**
- Has their own business entity (LLC or Corp)
- Works for multiple clients simultaneously
- Sets their own hours and methods
- Provides their own equipment and tools
- Can subcontract the work
- Has a project-based (not ongoing) relationship

**Red flags that signal employment:**
- Works exclusively for you
- Uses company equipment
- Reports to a manager daily
- Required to be available during specific hours
- Paid by the hour (vs. by deliverable)

---

## Privacy Law: GDPR, CCPA, and HIPAA

### GDPR (EU General Data Protection Regulation)

**Applies when:** You process personal data of EU/EEA residents, regardless of company location.

**Key obligations:**
- **Lawful basis**: One of six lawful bases required for each processing activity (consent, contract, legitimate interest most common for B2B SaaS)
- **Data minimization**: Collect only what you need
- **Right of access, erasure, portability**: Respond within 30 days
- **Data breach notification**: 72 hours to supervisory authority; without undue delay to affected individuals if high risk
- **DPA**: Required with every data processor (your cloud providers, analytics tools, AI APIs)

**Penalties:** Up to €20M or 4% of global annual turnover, whichever is higher. Maximum fine issued: €1.2B (Meta, Ireland DPC, 2023).

**Schrems III watch (2025):** EU-US Data Privacy Framework (successor to Privacy Shield and Schrems II) is currently valid but under legal challenge. If invalidated again, Standard Contractual Clauses (SCCs) are the fallback mechanism.

### CCPA/CPRA (California Consumer Privacy Act)

**Applies when:** For-profit business in California (or doing business with CA residents) that meets ONE of:
- Annual gross revenue > $25M
- Buys, sells, or shares personal information of 100,000+ consumers
- Derives 50%+ of revenue from selling personal information

**Key rights:** Right to know, right to delete, right to opt-out of sale/sharing, right to correct, right to limit use of sensitive personal information.

**AI-specific CPRA:** Automated decision-making that produces "significant effects" triggers opt-out rights as of January 2026 enforcement rules. If your AI agent makes hiring, lending, or housing decisions, automated decision-making opt-out must be offered.

**Penalty:** $2,500 per unintentional violation; $7,500 per intentional violation (CPPA enforcement).

### HIPAA (Health Insurance Portability and Accountability Act)

**Applies when:** You are a covered entity (healthcare provider, health plan, clearinghouse) or a **business associate** (vendor processing PHI on behalf of a covered entity).

**Business Associate Agreement (BAA):** Required before any PHI is shared with a vendor. OpenAI, Anthropic, Google offer BAAs for enterprise/healthcare tiers. Do not process PHI without a signed BAA.

**Safe harbor de-identification:** Remove 18 specific identifiers (name, DOB, SSN, geographic data below state level, etc.) to make data not PHI (Safe Harbor method). Expert determination method (statistical approach) is more flexible.

**Penalties (2024 update):** Tiered: $100–$50,000 per violation, up to $1.9M per violation category per year. Criminal penalties for willful neglect: up to $250,000 + 10 years imprisonment.

---

## AI Regulation: The Compliance Landscape

### EU AI Act (Regulation 2024/1689)

The world's first comprehensive AI regulation. **Risk-based framework:**

**Unacceptable risk (banned, effective February 2025):**
- Social scoring by governments
- Real-time remote biometric identification in public spaces (with narrow exceptions)
- Subliminal manipulation
- Exploitation of vulnerabilities of specific groups

**High-risk AI (compliance requirements, enforcement August 2026):**
- Categories: biometric identification, critical infrastructure, education/vocational training, employment/worker management, essential services (credit, insurance), law enforcement, migration/border control, justice/democracy
- Requirements: conformity assessment, CE marking, human oversight, transparency, accuracy requirements, technical documentation, registration in EU AI database

**Limited risk (transparency requirements only):**
- Chatbots must disclose they are AI
- AI-generated deepfakes must be labeled

**General purpose AI (GPAI) models (enforcement August 2025):**
- Systemic risk threshold: models trained on >10^25 FLOPs
- Affected: GPT-5, Claude Opus 4.8, Gemini 2.5 Pro
- Requirements: adversarial testing reports, incident reporting, technical documentation

**For startups:** If your AI agent is used for hiring, performance evaluation, or access to essential services, you are operating a high-risk AI system. Compliance timeline: begin now for August 2026 enforcement.

### US AI Executive Order (December 2025)

The Biden-era AI EO (October 2023) was revoked and replaced with a new EO in December 2025:
- Focus on AI competitiveness and deregulation
- National security carve-outs remain
- NIST AI Risk Management Framework (AI RMF) remains the US voluntary standard
- No comprehensive federal AI law as of June 2026 (preemption debate ongoing)

### NYC Local Law 144 (AEDT Law)

**Effective July 2023.** Requires employers using **automated employment decision tools (AEDTs)** in New York City to:
1. Conduct annual bias audits by an independent auditor
2. Publish bias audit results publicly
3. Notify candidates/employees that AEDTs are being used
4. Provide alternative selection process upon request

**Penalty:** $375 first violation; $1,500 per subsequent violation per day.

**Broadening:** Similar laws in Illinois, Maryland, and Colorado. EU AI Act covers the same territory at EU scale.

---

## Equity: SAFEs, Options, and QSBS

### SAFE (Simple Agreement for Future Equity)

The Y Combinator Post-Money SAFE is the standard pre-seed/seed instrument as of 2023–2026.

**Key terms:**
- **Valuation cap**: Maximum price at which SAFE converts to equity
- **Discount rate**: SAFE converts at [X]% discount to the next round price (typically 15–20%)
- **Post-money SAFE**: Dilution from the SAFE is allocated to founders/existing investors *before* the priced round (cap table math is cleaner)
- **Pro-rata rights**: Option to invest in the next priced round to maintain ownership percentage

**Conversion mechanics:**
```
SAFE converts at: min(cap / (post-money shares), qualified round price × (1 - discount))
```

**MFN (Most Favored Nation) clause:** A SAFE with MFN allows the holder to adopt the terms of any subsequently issued SAFE on more favorable terms. Creates a "floor" guarantee for early investors.

### Stock Options: ISO vs. NSO

| Feature | ISO | NSO |
|---|---|---|
| Who can receive | Employees only | Anyone (employees, contractors, advisors) |
| Strike price | Must be ≥ FMV at grant | Any price |
| Tax (exercise) | No regular income tax; AMT may apply | Ordinary income on spread |
| Tax (sale) | Long-term capital gains if 2/1 holding periods met | Capital gains on appreciation |
| Company deduction | None | Deduction on spread at exercise |

**ISO holding periods:** Must hold shares 2 years from grant AND 1 year from exercise for favorable tax treatment.

**83(b) election reminder:** For restricted stock (common for founders), file within 30 days of grant. For options, 83(b) on early exercise only — does not apply to unvested NSOs without early exercise.

### QSBS (§1202 Qualified Small Business Stock)

**The most powerful tax benefit for startup investors and founders:**
- Exclude up to **$10M** (or 10x adjusted basis) of gain from federal capital gains tax
- Holding period: 5+ years
- Company must be a C-Corp with aggregate gross assets ≤ $50M at time of issuance (includes VC-backed rounds)
- "Active business" requirement: must be in a qualifying trade or business (most software, AI, and tech companies qualify; finance, law, health, hospitality do NOT)
- New York and California do NOT conform to §1202 — state taxes still apply in those states

**Stacking:** Multiple investors in the same round can each claim the $10M exclusion. Founders, employees, and investors all independently eligible.

---

## M&A: Due Diligence Checklist

### Corporate and Governance

```
☐ Articles of Incorporation and all amendments
☐ Bylaws (current)
☐ Board and stockholder meeting minutes (all)
☐ Cap table (fully diluted), signed option agreements
☐ Voting agreements, co-sale rights, drag-along provisions
☐ Right of First Refusal (ROFR) and co-sale agreements
☐ Investor rights agreements (registration rights, information rights)
```

### Intellectual Property

```
☐ Patent portfolio (issued + pending), assignments recorded at USPTO
☐ Trademark registrations and pending applications
☐ Copyright registrations (if any)
☐ PIIA signed by all current and former employees/contractors
☐ Open-source license compliance (no AGPL ticking time bombs)
☐ Training data licenses and provenance documentation
☐ Trade secret protection measures documented
```

### Contracts

```
☐ Top 20 customer MSAs (revenue concentration risk)
☐ Key vendor/supplier agreements (cloud, AI APIs)
☐ Employment agreements (key employees, change-of-control provisions)
☐ Advisor agreements (IP assignment, equity)
☐ Partnership and reseller agreements
```

### Employment and Benefits

```
☐ Employee headcount by state (tax nexus implications)
☐ Contractor classification review (AB5 exposure in CA)
☐ Equity grants (vesting, acceleration, double-trigger)
☐ Workers' compensation and unemployment insurance
☐ 409A valuations (all historical, supports option strike prices)
```

### Regulatory and Compliance

```
☐ Data privacy compliance (GDPR DPA, CCPA notices, HIPAA BAAs if applicable)
☐ AI regulation compliance (EU AI Act, NYC LL144, EEOC AI guidance)
☐ Export controls (EAR/ITAR compliance if selling AI to foreign buyers)
☐ Money transmission licenses (if handling payments)
☐ State licensing (if operating in regulated industries)
```

### Financial

```
☐ Financial statements (3 years, audited if available)
☐ Tax returns (federal + state, 3 years)
☐ Outstanding liabilities (loans, convertible notes, SAFEs)
☐ Revenue recognition policy (ASC 606 compliance)
☐ Deferred revenue schedule
```

---

## Practical Contract Protections for AI Products

### AI-Specific Contract Provisions (2025 Standard)

**Training prohibition clause (vendor to customer):**
```
Customer shall not use, and shall not permit third parties to use, the 
Service, Platform, or any outputs thereof, directly or indirectly, for 
the purpose of training, fine-tuning, or improving any machine learning 
model, algorithm, or artificial intelligence system without Vendor's 
prior written consent.
```

**AI output disclaimer:**
```
Outputs generated by the AI components of the Service are provided for 
informational purposes only. Outputs are not guaranteed to be accurate, 
complete, or suitable for any particular purpose. Customer is solely 
responsible for reviewing and validating all Outputs before relying on 
them for any business, legal, financial, medical, or other consequential 
purpose.
```

**Model drift / performance degradation:**
Include explicit language that vendor may update underlying models and that SLAs apply to uptime, not output quality (unless you're selling a quality-guaranteed output product — then define quality precisely with objective metrics).
