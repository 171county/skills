---
name: business-tech-law-expert
description: Expert-level business and technology law for tech companies — corporate structure (Delaware C-corp, QSBS), SaaS contract architecture (MSA/DPA/SLA), data protection (GDPR/CCPA/CPRA/state laws), AI regulation (EU AI Act, US AI governance), cybersecurity law (SEC rules, HIPAA, SOC 2), employment law, export controls, and international expansion legal frameworks. Use when advising on entity structure, contract terms, data compliance, AI regulatory obligations, or legal risk in technology products.
---

# Business Tech Law Expert

> **Disclaimer**: This skill provides legal information, not legal advice. Consult qualified legal counsel for specific matters.

## Corporate Structure

### Delaware C-Corporation (Standard for VC-Backed)

**Why Delaware**
- DGCL (Delaware General Corporation Law): most flexible, business-friendly
- Court of Chancery: specialized business court; judge-decided (no jury); highly predictable
- Venture capital standard: institutional investors require C-corp for preferred stock
- IPO: underwriters expect Delaware C-corp

**Franchise Tax Trap**
- Delaware charges annual franchise tax based on authorized shares
- Default method (Authorized Shares): 10M authorized shares → ~$85,000/year
- **Assumed Par Value Capital Method (APVCM)**: use this instead
  - Based on total assets, not authorized shares
  - Typical result: $400-$500/year for most startups
  - Must elect APVCM; not applied automatically; file with annual report

**S-Corp vs C-Corp**
| Feature | S-Corp | C-Corp |
|---------|--------|--------|
| Pass-through taxation | Yes | No (double tax) |
| Preferred stock | No | Yes |
| Foreign shareholders | No | Yes |
| VC investable | No | Yes |
| QSBS eligibility | No | Yes |
| Conversion later | Possible (costs) | N/A |

### Fundraising Legal Framework

**SAFE Notes (Current Standard)**
- Post-money SAFE (YC standard): valuation cap set on post-money basis
- **YC S25 investment**: $125K at 7% + $375K uncapped MFN SAFE
- No maturity date; no interest; no debt — favorable balance sheet treatment
- Side letter: additional rights (information rights, co-sale) alongside SAFE

**Series A — NVCA Term Sheet Standards (2025)**
- Liquidation preference: 1x non-participating (98% of deals)
- Anti-dilution: broad-based weighted average
- Board: 2 founders + 1 lead VC + 2 independent = 5-person standard
- NVCA Model Documents: standard Series A stack (CofI, IRA, VA, ROFR/CoSale, SPA)

**QSBS (Section 1202) — OBBBA July 2025**
- Exclusion cap: $15M per taxpayer per company (raised from $10M)
- Asset threshold: $75M gross assets at issuance (raised from $50M)
- Holding period: 5 years
- Requirements: C-corp, original issuance, active business, qualified trade
- **Planning**: structure IP in C-corp entity from Day 1

---

## SaaS Contract Architecture

### Master Service Agreement (MSA) / Order Form Structure

**Document Hierarchy**
```
MSA (governing terms) → Order Form (commercial terms) → SOW (if applicable)
  + DPA (Data Processing Agreement)
  + SLA (Service Level Agreement)
  + AUP (Acceptable Use Policy)
```

**MSA Key Provisions**

**Limitation of Liability**
- Direct damages cap: typically 12 months of fees paid
- **Mutual** cap preferred; customers often push for uncapped customer liability
- Carve-outs from cap (common): IP infringement, data breach, willful misconduct, death/bodily injury, confidentiality breach
- **SaaS vendor position**: cap at 12 months; mutual cap; carve-outs only for enumerated categories

**Intellectual Property**
- Customer data: customer owns; vendor gets limited license to provide service
- Aggregated/anonymized data: vendor typically retains rights to use for service improvement
- Feedback/suggestions: vendor owns IP in any feedback customer provides
- Deliverables in SOW: specify ownership; work-for-hire vs. license-back to vendor

**Warranty Disclaimers**
- Disclaim: implied warranties of merchantability, fitness for particular purpose, non-infringement
- Express warranty: service performs materially as described in documentation
- Uptime warranty: typically stated in SLA, not MSA

**Auto-Renewal Provisions**
- Standard: 30-60 days written notice to cancel before renewal
- Regulation Watch (2025-2026): multiple states passing auto-renewal notification laws (CA, NY, IL, CO)
- Requirements: prominent disclosure at purchase; reminder notice before renewal; easy cancellation mechanism
- FTC Click-to-Cancel rule (effective 2025): cancellation must be as easy as sign-up for subscription services

**Termination for Cause**
- Material breach: 30-day cure period (typically)
- Insolvency/bankruptcy: immediate right to terminate
- Non-payment: 10-15 business days cure

**Termination for Convenience**
- Enterprise customers: often demand termination for convenience with pro-rata refund
- Vendor position: only if annual contract; no mid-term cancellation for monthly

---

## Data Protection Law

### GDPR (EU General Data Protection Regulation)

**Extraterritorial Scope**
- Applies to any company processing data of EU residents, regardless of company location
- Threshold: offering goods/services to EU residents OR monitoring behavior of EU residents

**Legal Bases for Processing**
1. **Consent**: freely given, specific, informed, unambiguous; withdrawal as easy as giving
2. **Contractual necessity**: necessary to perform contract with the individual
3. **Legitimate interests**: balancing test; must document legitimate interests assessment (LIA)
4. **Legal obligation**: compliance with EU law
5. **Vital interests**: emergency scenarios
6. **Public task**: public authority functions

**Key Obligations**
- Privacy notices: purpose, legal basis, retention period, rights, transfers
- Data Subject Rights: access (DSAR), rectification, erasure, portability, object to processing, restrict processing
- DSAR response: 30 days (extensible to 90 days for complex requests)
- Data Protection Impact Assessment (DPIA): required for high-risk processing
- Data breach notification: 72 hours to supervisory authority; without undue delay to affected individuals (if high risk)
- DPO: required for large-scale processing of special category data; public authorities; large-scale monitoring

**International Transfers (Post-Schrems II)**
- EU-US Data Privacy Framework (DPF): adequate since July 2023; certification required; ongoing challenge risk
- Standard Contractual Clauses (SCCs): 2021 SCCs + Transfer Impact Assessment (TIA) required
- Adequacy decisions: UK (temporary), Switzerland, Canada, Japan, Israel
- Binding Corporate Rules (BCRs): for intra-group transfers; expensive to implement

### US Data Protection

**CCPA / CPRA (California)**
- Applies: businesses with $25M+ annual revenue OR 50,000+ consumer records OR 50%+ revenue from selling personal info
- Consumer rights: know, delete, opt-out (sale/sharing), correct, limit use of sensitive personal info
- CPRA effective Jan 2023; CCPA Privacy Regulations fully effective March 2024
- **ADMT (Automated Decision-Making Technology)** regulations: effective 2026; opt-out right for significant decisions; access to logic; notice requirements for profiling
- Enforcement: California Privacy Protection Agency (CPPA) + AG; no private right of action (except data breach)

**State Privacy Law Landscape (2026)**
- 20+ states have comprehensive privacy laws: Virginia (VCDPA), Colorado (CPA), Connecticut, Texas, Iowa, Montana, Oregon, New Hampshire, New Jersey, Delaware, Indiana, Tennessee, Maryland, Minnesota, Nebraska, Rhode Island, Kentucky
- Requirements vary; most follow CCPA/GDPR hybrid model
- Practical approach: implement GDPR-compliant practices; covers most state requirements

**HIPAA (Health Insurance Portability and Accountability Act)**
- Applies: covered entities (healthcare providers, insurers) + business associates
- BAA (Business Associate Agreement): required before vendor accesses PHI
- SaaS selling to healthcare: must sign BAA with covered entity customers; your product must be HIPAA-compliant
- PHI: 18 categories of individually identifiable health information
- Breach notification: 60 days to HHS; without unreasonable delay to individuals; media notice if >500 in a state

**FERPA (Family Educational Rights and Privacy Act)**
- Student education records; applies to institutions receiving federal funding
- "School official" exception: ed-tech companies can access records as "school official" under written agreement
- Requires: legitimate educational interest; directory info opt-out

**COPPA (Children's Online Privacy Protection Act)**
- Applies: online services directed to children under 13, or with actual knowledge of users under 13
- Requires: verifiable parental consent before collecting data from under-13 users
- Age-gating: must have reasonable age verification
- FTC enforcement: significant fines; TikTok $5.7M (2019), Epic Games $520M (2022)

---

## AI Regulation

### EU AI Act (In Force)

**Tier 1 — Prohibited AI (Effective February 2, 2025)**
- Social scoring by public authorities
- Real-time remote biometric identification in public spaces (with exceptions for law enforcement)
- Subliminal manipulation techniques
- AI that exploits vulnerabilities of persons
- Untargeted scraping of facial images for recognition databases
- Emotion recognition in workplaces/educational institutions (with exceptions)

**Tier 2 — GPAI (General Purpose AI) Obligations (Effective August 2025)**
- Applies to: providers of GPAI models (foundation models)
- **All GPAI providers**:
  - Technical documentation
  - Copyright compliance policy; training data summaries
  - Transparency to downstream providers
- **High-capability GPAI** (systemic risk; >10^25 FLOPs training compute):
  - Additional: adversarial testing; incident reporting to AI Office; cybersecurity measures
  - Model evaluation against state-of-the-art benchmarks
- **Code of Practice**: GPAI Code of Practice finalized July 2025; voluntary initially; will inform enforcement

**Tier 3 — High-Risk AI Systems (Effective August 2, 2026)**
- Categories: critical infrastructure, education/vocational training, employment decisions, access to essential services, law enforcement, migration/asylum, justice, democratic processes
- Requirements: risk management system; data governance; technical documentation; transparency; human oversight; accuracy/robustness
- Conformity assessment before market placement
- CE marking required; registration in EU database
- **Note**: many B2B SaaS AI features that affect employment decisions or access to services will be high-risk

**Fines**
- Prohibited AI violations: €35M or 7% global annual turnover
- High-risk non-compliance: €15M or 3% global annual turnover
- Incorrect information to authorities: €7.5M or 1.5% turnover

### US AI Governance (2025-2026)

**Biden EO 14110 (October 2023) → Revoked January 2025**
- Trump EO 14179 (January 2025): "Removing Barriers to American Leadership in AI"
- Policy shift: deregulatory; prioritize US AI competitiveness; rescind Biden-era requirements

**America's AI Action Plan (July 2025)**
- Released by OSTP; priority areas: federal AI procurement, export competitiveness, research infrastructure
- NSF funding increases; NIST AI Risk Management Framework guidance

**State AI Laws**
- **Colorado AI Act (SB 205)**: effective June 30, 2026; applies to "high-risk AI systems" making consequential decisions in employment, housing, credit, healthcare, education, legal services; requires bias testing, impact assessments, consumer notice, right to appeal; only state law with comprehensive scope
- **Illinois BIPA (Biometric Information Privacy Act)**: $1,000/negligent violation, $5,000/intentional; private right of action (unique); facial recognition, fingerprints, voiceprints, iris scans; written consent required before collection; written retention policy
- **New York City Local Law 144**: bias audits for AEDT (Automated Employment Decision Tools); public notice to candidates; annual bias audit results publication
- **Texas AI Act (HB 149)**: signed 2025; consequential decisions; impact assessments; notice requirements

---

## Cybersecurity Law

### SEC Cybersecurity Disclosure Rules (Effective Dec 2023)

**Form 8-K (Material Incident — Public Companies)**
- Timeline: 4 business days after determining incident is material
- Content: nature, scope, timing, material impact or reasonably likely material impact
- **Materiality standard**: reasonable investor would consider it important; don't delay while investigating
- Exception: national security/public safety (coordination with FBI/AG)

**Form 10-K (Annual Disclosure)**
- Risk factors: cybersecurity risk management, strategy, governance
- Board oversight: describe how board oversees cybersecurity risk
- Management's role: describe management's role in cybersecurity

### SOC 2

**Type I**: controls designed effectively at a point in time
**Type II**: controls operating effectively over a period (typically 6-12 months)
- Type II required by enterprise customers in SaaS contracts

**Trust Service Criteria**:
1. Security (CC criteria) — required
2. Availability — optional
3. Processing Integrity — optional
4. Confidentiality — optional
5. Privacy — optional

### Export Controls

**EAR (Export Administration Regulations)**
- ECCN 4E091: AI model weights classification (effective January 2025, BIS update)
- Applicable to: export of AI model weights, software, technology with military/dual-use application
- Destinations: controlled countries list; Russia, China, Iran require special licensing
- **Startup relevance**: open-sourcing model weights, API access from restricted countries, cloud services to restricted parties

**ITAR (International Traffic in Arms Regulations)**
- Defense tech companies: register with DDTC before any export of ITAR-controlled items
- Even academic sharing, employment of foreign nationals on defense projects = ITAR compliance

---

## Employment Law for Tech Companies

**Non-Compete Status (2026)**
- FTC rule vacated (Feb 2026); state law governs
- Enforceable with restrictions: TX, FL, MA, NY (executives only post-2024)
- Not enforceable: CA, ND, OK, MN + growing list

**Worker Classification (Employee vs. Contractor)**
- Federal: economic reality test (control, profit/loss, integration, permanence)
- California AB5 (Borello + ABC test): much stricter; strong presumption of employment
- DOL 2024 rule: clarified multi-factor economic reality test for federal purposes
- **Risk**: misclassified contractors → back wages, benefits, taxes, penalties

**Remote Work Legal Issues**
- **Tax nexus**: employees in a state create corporate income tax + payroll withholding obligations
- **State registration**: may need to register as foreign corporation in employee's state
- Solution: employer of record (EOR) services (Deel, Remote, Rippling) for cross-state/international employees

---

## International Expansion Legal Framework

### China

**PIPL (Personal Information Protection Law)**
- Effective: November 2021; enforcement ramping
- Security assessment required: export of personal information of >1 million Chinese individuals
- China Standard Contract: must use China SCCs (not EU SCCs) for transfers out of China
- Data localization for "critical information infrastructure operators"
- CAC (Cyberspace Administration of China): primary enforcement body

### India

**DPDP (Digital Personal Data Protection Act, 2023)**
- Rules finalized: November 2025
- Full enforcement expected: May 2027
- Requirements: notice, consent-based processing for most personal data
- Data fiduciaries (large processors): additional obligations
- Data localization: government retains power to restrict transfers; not yet implemented
- Children's data: parental consent required; no behavioral tracking of minors

### EU Specifics

**AI Act compliance** (see above)
**NIS2 Directive**: cybersecurity requirements for essential/important entities; network security, incident reporting
**ePrivacy** (Regulation pending): cookie law update; consent requirements for tracking

---

## Expert Calibration Notes

- **Delaware franchise tax is the most common expensive mistake**: every Delaware C-corp should immediately elect the Assumed Par Value Capital Method; the default authorized shares method charges ~$0.01-0.01 per authorized share/year
- **Auto-renewal FTC click-to-cancel rule (2025)** creates compliance obligation for all SaaS subscription services — review cancellation flows against "as easy as sign-up" standard
- **GDPR is not just EU companies' problem**: US SaaS with any EU customers needs a DPA, EU SCCs with transfer impact assessments, and a lawful basis for every processing operation
- **HIPAA BAA is a contractual gate, not a compliance pass**: signing a BAA requires your product to actually be HIPAA-compliant; many SaaS vendors sign BAAs without building the required technical safeguards
- **EU AI Act high-risk classification scope is broader than most expect**: AI features affecting employment decisions (resume screening, performance monitoring, scheduling) qualify as high-risk with August 2026 compliance deadlines
- **Colorado AI Act is the state law to track**: first comprehensive state AI law with real enforcement teeth; other states watching its implementation; proactive compliance positions you for national expansion
- **Export control for AI model weights** (ECCN 4E091) is new territory: open-source model releases and API access from sanctioned country IPs now require legal review before publication
