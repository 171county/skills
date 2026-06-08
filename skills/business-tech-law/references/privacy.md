# Privacy Law Reference

## GDPR: General Data Protection Regulation

**Applicable law:** Regulation (EU) 2016/679, effective May 25, 2018. Extraterritorial scope: applies to any organization processing personal data of EU residents, regardless of where the organization is established (Art. 3).

### Lawful Basis (GDPR Art. 6)

Processing must rest on exactly one lawful basis. Common bases for tech companies:

| Basis | When to Use | Key Limitation |
|---|---|---|
| **Consent** (Art. 6(1)(a)) | Marketing, optional features, AI training | Must be freely given, specific, informed, unambiguous; withdrawable at any time without detriment |
| **Contract** (Art. 6(1)(b)) | Processing to deliver the service the user contracted for | Strictly scoped to what is necessary; cannot use for analytics/marketing |
| **Legitimate Interests** (Art. 6(1)(f)) | Analytics, fraud prevention, security | Requires LIA (Legitimate Interests Assessment); does not apply to public authorities; must pass balancing test |
| **Legal Obligation** (Art. 6(1)(c)) | KYC/AML, tax records, court orders | Only where an actual legal obligation exists |
| **Vital Interests** (Art. 6(1)(d)) | Emergency scenarios | Narrow; use only when data subject is incapacitated |

**Special category data (Art. 9):** Race/ethnicity, political opinions, religious beliefs, trade union membership, genetic data, biometric data for ID purposes, health data, sex life/orientation. Requires a separate Art. 9(2) condition in addition to an Art. 6 lawful basis. Most common: explicit consent (Art. 9(2)(a)) or necessity for health purposes (Art. 9(2)(h)).

### Data Subject Rights

| Right | GDPR Article | Timeframe | Notes |
|---|---|---|---|
| Access (SAR) | Art. 15 | 1 month (extendable +2 months for complex requests) | Must provide copy of data; cannot charge unless manifestly excessive |
| Rectification | Art. 16 | 1 month | Correct inaccurate data; complete incomplete data |
| Erasure ("Right to be Forgotten") | Art. 17 | 1 month | Only applies where lawful basis no longer holds or consent withdrawn; not absolute |
| Restriction | Art. 18 | Without undue delay | Suspends processing while accuracy is contested |
| Portability | Art. 20 | 1 month | Machine-readable format; only applies to automated processing based on consent or contract |
| Objection | Art. 21 | Without undue delay | Must stop processing unless compelling legitimate grounds; absolute right for direct marketing |
| Automated Decision-Making | Art. 22 | Triggered by ADM | Right not to be subject to solely automated decisions with significant effects; must offer human review |

**Implementation:** Maintain a formal DSR (Data Subject Request) workflow. Log all requests and responses. Verify identity before disclosing data (but don't use identity verification as a barrier — must be proportionate to risk).

### Data Processing Agreements

See `contracts.md` — DPA section for detailed requirements under Art. 28. The DPA is a compliance obligation, not merely a commercial contract.

### Cross-Border Data Transfers (Art. 44–49)

Personal data may not be transferred outside the EEA/UK unless one of the following applies:

**1. Adequacy Decision (Art. 45):** European Commission has determined the destination country ensures an adequate level of protection. As of 2025–2026, adequacy covers: Andorra, Argentina, Canada (commercial organizations under PIPEDA), Faroe Islands, Guernsey, Israel, Isle of Man, Japan, Jersey, New Zealand, South Korea, Switzerland, UK (post-Brexit), Uruguay, and US (partially, under the EU-US Data Privacy Framework — DPF).

**EU-US Data Privacy Framework (DPF):** Adopted July 2023, replacing Privacy Shield (invalidated by Schrems II in July 2020). US companies self-certify with the US Department of Commerce. Risk: DPF faces ongoing legal challenges (Privacy International filed challenge in 2023; potential Schrems III risk). Companies relying solely on DPF should maintain SCCs as a backup.

**2. Standard Contractual Clauses (SCCs) (Art. 46(2)(c)):** The primary transfer mechanism for non-adequate countries. European Commission adopted new SCCs in June 2021 (Commission Implementing Decision 2021/914), replacing the old 2010 sets. Four modules:
- Module 1: Controller-to-Controller
- Module 2: Controller-to-Processor (most common for cloud/SaaS)
- Module 3: Processor-to-Processor
- Module 4: Processor-to-Controller

**Transfer Impact Assessment (TIA) requirement (post-Schrems II):** When using Art. 46 safeguards (including SCCs), organizations must assess whether destination country laws (particularly government surveillance capabilities) undermine the protection. The TIA involves: (a) mapping the transfer, (b) identifying the transfer tool used, (c) assessing destination country law, (d) implementing supplementary measures (encryption, pseudonymization, contractual commitments) if necessary.

**Key TIA red flags:** Transfers to the US (NSA/FISA 702), China (National Intelligence Law Art. 7), Russia, UAE. For US transfers, assess whether the specific recipient's data is likely to be targeted by intelligence services.

**3. Binding Corporate Rules (BCRs) (Art. 47):** Internal codes of conduct approved by a lead supervisory authority (typically the CIPA, ICO, or CNIL) for intra-group transfers within multinational companies. BCRs provide full transfer authorization without SCCs for intra-group transfers. Approval process takes 2+ years. Suitable for large multinationals; not practical for startups.

**4. Narrow Derogations (Art. 49):** Explicit consent of the data subject; contract performance; vital interests; important reasons of public interest; establishment/exercise/defense of legal claims. These are exceptions, not regular transfer mechanisms — cannot be used systematically.

### GDPR Enforcement Track Record

- **Meta (2023):** €1.2 billion (Irish DPC) for unlawful SCCs in US-EU data transfers
- **Meta (2022):** €390 million for reliance on "contractual necessity" for behavioral advertising
- **Amazon (2021):** €746 million (Luxembourg CNPD) for cookie consent failures
- **WhatsApp (2021):** €225 million (Irish DPC) for transparency failures

**Enforcement pattern:** The Irish DPC handles most major Big Tech cases (Meta, Google, Apple EU headquarters). Austrian, Dutch, and French DPAs are aggressive on cookie consent and tracking. German DPAs (Länder-level) are active on employee data and B2B processing.

---

## CCPA / CPRA (California)

**Applicable law:** California Consumer Privacy Act (Cal. Civil Code § 1798.100 et seq.), as amended by the California Privacy Rights Act (CPRA), Prop 24 (2020), effective January 1, 2023. Enforced by: California Attorney General (AG) + California Privacy Protection Agency (CPPA).

**Threshold:** Applies to for-profit businesses doing business in California that meet one or more of: (a) $25M+ annual gross revenue, (b) buy/sell/share personal information of 100,000+ consumers/households annually, (c) derive 50%+ of annual revenue from selling/sharing personal information.

### Consumer Rights Under CCPA/CPRA

| Right | Code Section | Notes |
|---|---|---|
| Right to Know | § 1798.100 | What categories and specific pieces of PI collected, sources, business purposes, third parties |
| Right to Delete | § 1798.105 | Subject to exceptions (legal obligation, security research, etc.) |
| Right to Correct | § 1798.106 (CPRA) | Added by CPRA; correct inaccurate PI |
| Right to Opt-Out of Sale/Sharing | § 1798.120 | Must have "Do Not Sell or Share My Personal Information" link on homepage |
| Right to Limit Use of Sensitive PI | § 1798.121 (CPRA) | Separate from opt-out; must have "Limit the Use of My Sensitive Personal Information" link |
| Right to Non-Discrimination | § 1798.125 | Cannot deny service, charge different prices, or provide inferior service for exercising rights |
| Right to Data Portability | § 1798.100(d) | Two or more times per year; specific format |

### Sensitive Personal Information (CPRA)

CPRA created a new heightened category (§ 1798.140(ae)): SSNs, driver's license/state ID/passport numbers, financial account credentials, precise geolocation, racial/ethnic origin, religious/philosophical beliefs, union membership, content of mail/email/text, genetic data, biometric data for ID purposes, health/sex life/sexual orientation data.

**Key distinction from GDPR:** CCPA/CPRA does not prohibit processing SPI — it gives consumers the right to *limit* use to what is necessary to perform the requested service or as reasonably expected.

### Sale vs. Sharing (CPRA)

CPRA added "sharing" as a separate concept to capture cross-context behavioral advertising (passing data to ad networks), even without monetary consideration. "Sale" + "sharing" opt-out rights are triggered separately from "limit SPI use." Use a unified "Do Not Sell or Share My Personal Information" signal.

**Global Privacy Control (GPC):** CPPA regulations require businesses to honor GPC signals (browser-level opt-out) as equivalent to the consumer clicking "Do Not Sell or Share." Implement GPC recognition in cookie consent management systems.

### CPPA Enforcement (2024–2025)

The CPPA (independent from the AG) began enforcement in July 2023. Key actions:
- Honda (2024 settlement): $632,500 for requiring consumers to provide excessive personal information to exercise opt-out rights
- Doordash (2024 settlement): $375,000 for selling consumer data without proper notice
- CPPA adopted new ADMT (automated decision-making technology) regulations effective January 1, 2026, including opt-out rights for profiling and annual risk assessments for high-risk uses

**No cure period:** CPRA removed the 30-day cure period. Businesses are strictly liable for violations discovered by the CPPA.

---

## HIPAA (Health Insurance Portability and Accountability Act)

**Applicable law:** 45 CFR Parts 160, 162, and 164. Enforced by HHS Office for Civil Rights (OCR).

**Who is covered:**
- **Covered Entities (CEs):** Healthcare providers that transmit health information electronically, health plans, healthcare clearinghouses
- **Business Associates (BAs):** Vendors/contractors that create, receive, maintain, or transmit PHI on behalf of CEs (includes SaaS vendors, cloud providers, analytics companies, billing services, AI health companies)
- **Subcontractors:** BAs of BAs — also covered; flow-down requirements apply

**Protected Health Information (PHI):** Individually identifiable health information in any form (electronic, paper, oral) that relates to past, present, or future physical/mental health, provision of healthcare, or payment for healthcare.

### HIPAA Safeguards for Health Tech

**Privacy Rule (45 CFR Part 164, Subpart E):**
- Minimum necessary standard: only use/disclose the minimum PHI necessary for each purpose
- Notice of Privacy Practices: patients must receive description of how PHI is used
- Authorization requirements: most disclosures outside treatment/payment/operations require signed authorization

**Security Rule (45 CFR Part 164, Subpart C):**
- Administrative safeguards: risk analysis, workforce training, contingency planning
- Physical safeguards: facility access controls, workstation security
- Technical safeguards: access controls, audit controls, integrity controls, transmission security
- **2025 Proposed Security Rule Updates:** HHS proposed significant updates (published Jan. 6, 2025) including: mandatory annual risk analysis, MFA requirement, 72-hour technology asset inventory, incident response within specified timeframes, mandatory encryption of ePHI at rest and in transit, annual compliance audits. Final rule expected summer 2026.

**Business Associate Agreements (BAAs):**
- Mandatory before sharing PHI with any BA (45 CFR § 164.504(e))
- Must include: permitted uses/disclosures, obligation to implement safeguards, breach notification duties, deletion/return of PHI upon termination, flow-down to subcontractors
- **New proposed requirement:** BAs must verify their technical safeguards via annual written analysis by a subject matter expert

**Breach Notification Rule (45 CFR §§ 164.400–414):**
- CE must notify affected individuals within 60 days of discovering a breach
- CE must notify HHS Secretary: within 60 days for breaches involving 500+ individuals; annually for smaller breaches
- Breaches involving 500+ individuals in one state trigger media notification
- BA must notify CE without unreasonable delay (and within 60 days of discovery)

**HITECH Act enhancements (2009):** Expanded BA liability to include direct enforcement. OCR can impose civil monetary penalties directly on BAs. Penalty tiers: $100–$50,000 per violation based on culpability; annual maximum $1.9M per violation category.

---

## COPPA (Children's Online Privacy Protection Act)

**Applicable law:** 15 U.S.C. § 6501 et seq.; implementing regulations at 16 CFR Part 312. Enforced by FTC.

**Scope:** Operators of websites and online services directed to children under 13, OR operators with actual knowledge they are collecting PI from children under 13.

**"Directed to children" test:** Mix of factors including subject matter, visual content, music, animated characters, celebrities appealing to children, advertising targeting children, and composition of actual user base.

**Key obligations:**
- Post a clear and comprehensive privacy policy
- Provide direct notice to parents and obtain verifiable parental consent (VPC) before collecting PI from children
- Give parents the ability to review, delete, and refuse further collection of their child's PI
- Reasonable security for children's PI

### COPPA Rule Updates (Effective June 23, 2025; Compliance Deadline April 22, 2026)

The FTC finalized significant COPPA Rule amendments (approved January 16, 2025):

1. **Separate parental consent for targeted advertising:** Operators must obtain separate and additional VPC before sharing children's PI with third-party advertisers. Cannot bundle this consent with general service consent.
2. **Data retention limits:** Retain children's PI only as long as reasonably necessary to fulfill the specific purpose for which it was collected. No indefinite retention.
3. **Definition expansions:** "Personal information" now includes government-issued identifiers and biometric identifiers usable for automated recognition.
4. **Safe harbor transparency:** COPPA Safe Harbor programs must publicly disclose membership lists and report additional information to FTC.

**FTC enforcement:** FTC has imposed substantial COPPA penalties: YouTube/Google ($170M, 2019), TikTok ($5.7M, 2019), Epic Games ($275M, 2022). Average enforcement action: ~$50M+ for major platforms.

---

## US State Privacy Law Patchwork

As of mid-2026, 20+ states have enacted comprehensive privacy laws. The patchwork requires a multi-state compliance approach for any national consumer-facing business.

### Key State Laws Summary

| State | Law | Effective | Threshold | Private Right of Action |
|---|---|---|---|---|
| California | CCPA/CPRA | Jan 1, 2023 | $25M revenue OR 100K consumers | Limited (data breach only) |
| Virginia | CDPA | Jan 1, 2023 | 100K consumers or 25K + 50% revenue | No (AG enforcement) |
| Colorado | CPA | July 1, 2023 | 100K consumers or 25K + revenue | No (AG enforcement) |
| Connecticut | CTDPA | July 1, 2023 | 100K consumers or 25K + 50% revenue | No (AG enforcement) |
| Texas | TDPSA | July 1, 2024 (most provisions) | Any business in Texas (broad) | No (AG enforcement) |
| Florida | FDBR | July 1, 2024 | $1B+ global revenue (very narrow) | No |
| Oregon | OCPA | July 1, 2024 | 100K consumers | No |
| Montana | MCDPA | Oct 1, 2024 | 50K consumers | No |

**Texas Responsible AI Governance Act (effective Jan 1, 2026):** Prohibits AI systems encouraging self-harm, criminal activity, or unlawful discrimination; requires internal AI risk governance documentation available to Texas AG on request.

### Common Pattern Across State Laws

Most non-California laws follow the Virginia/GDPR-influenced model:
- Consumer rights: access, correction, deletion, portability, opt-out of targeted advertising, opt-out of profiling
- Data protection assessments required for high-risk processing (profiling, targeted advertising, sensitive data)
- Processor contracts required (analogous to GDPR DPAs)
- No private right of action (AG enforcement only, except California's limited breach PRIVA)

**Key divergence from GDPR:** No explicit lawful basis requirement; opt-out model (not opt-in) for targeted advertising; no DPO requirement.

### Multi-State Compliance Strategy

1. Implement CCPA/CPRA as your baseline — it is the most comprehensive
2. Layer Texas, Colorado, Connecticut, and Virginia requirements on top
3. Deploy a Universal Opt-Out Mechanism (UOOM) that responds to GPC signals
4. Ensure data subject request workflows cover all applicable rights
5. Update privacy notices to disclose all applicable state-specific rights
6. Conduct DPIAs/risk assessments for high-risk processing activities
7. Review and update vendor/processor contracts for state-law processor requirements
