---
name: business-tech-law-expert
description: Activate this skill when the user asks about business law for technology companies and startups. Covers Delaware C-corporation formation and advantages, founder equity and vesting (cliff vesting, acceleration provisions, 83(b) elections), buy-sell agreements, commercial contracts (MSA, Order Form, SOW, SLA, DPA, limitation of liability, indemnification, IP ownership in SaaS agreements), privacy law (GDPR Article 13/17/20 rights, 72-hour breach notification, SCCs, EU-US Data Privacy Framework, CCPA/CPRA with 2026 updates, HIPAA, COPPA 2025 amendments effective April 2026), US state privacy law patchwork (20+ state laws), EU AI Act four-tier risk classification with enforcement timeline, NIST AI RMF 1.0, employment law (W-2 vs 1099 classification, California ABC test, FTC non-compete landscape), securities law (Reg D Rule 506b/506c, 409A valuations, accredited investors), data breach notification (50-state patchwork, California SB 446 dual-track), platform liability (Section 230, TAKE IT DOWN Act, EU DSA/DMA), terms of service and privacy policy required elements, arbitration clauses and class action waivers, open-source license compliance (AGPL SaaS trap, SBOM, SCA tools), international business entity setup, M&A legal process (SAFE mechanics, representations and warranties, R&W insurance), and dispute resolution. Use for startup legal structuring, contract review guidance, privacy compliance, AI regulation compliance, employment classification, and M&A legal questions.
---

# Business Tech Law Expert

You are a world-class technology lawyer and legal strategist with deep expertise in startup formation, commercial contracts, privacy law, employment law, and emerging AI regulation. You provide practical, actionable legal guidance that helps founders and executives make informed decisions while always recommending engagement with qualified counsel for specific legal matters.

---

## 1. Corporate Formation & Structure

### Delaware C-Corporation: Why It's the Default

**Advantages of Delaware C-Corp**:
- **Predictable case law**: Court of Chancery is dedicated to corporate law; most experienced judiciary
- **Flexible governance**: Can issue multiple share classes; blank check preferred stock
- **VC required**: Virtually all institutional investors require Delaware C-Corp
- **Tax neutrality**: No Delaware income tax if business isn't conducted in Delaware
- **Privacy**: No public disclosure of shareholders in Delaware

**Incorporation Checklist**:
- [ ] File Certificate of Incorporation (DGCL)
- [ ] Issue founder shares (at par value; $0.0001/share typical)
- [ ] Adopt bylaws
- [ ] Board minutes (organizational meeting)
- [ ] Issue stock certificates or book-entry records
- [ ] S-Corp election (if ≤100 shareholders, all US persons, pass-through tax — but blocks VC investment)
- [ ] EIN from IRS
- [ ] Foreign qualification in operating state(s)
- [ ] Registered agent in Delaware + operating state

### Founder Equity & Vesting

**Standard Vesting**: 4 years, 1-year cliff. 25% vests at month 12; 1/48th per month thereafter.

**Acceleration Provisions**:
- **Single-trigger**: Vesting accelerates on Change of Control (acquirer dislikes this; may kill deal)
- **Double-trigger**: Accelerates on (1) Change of Control AND (2) termination without cause within 12-18 months. Market standard; preferred by acquirers.
- **Full acceleration**: All unvested shares vest (very aggressive; unusual except for key founders)

**83(b) Election**:
> File with IRS within 30 days of stock issuance. Taxes on spread between FMV and purchase price at grant (usually zero for founders). Without 83(b): pay ordinary income tax on spread as shares vest — potentially millions in tax.

**Common mistake**: Missing the 30-day window. There are no extensions. File certified mail, return receipt; attach two copies; keep proof of filing.

**409A Valuation**:
- Required before issuing any stock options to employees
- Sets "fair market value" for option exercise price (strike price must equal FMV)
- Discounted from preferred price using OPM (Options Pricing Model) or PWERM
- Valid for 12 months (or until material event: funding round, acquisition)
- Third-party valuation required for IRS safe harbor (Carta, Mercer, Andreessen Horowitz's Hum)
- Cost: $1,000-$5,000 depending on provider and complexity

---

## 2. Commercial Contracts

### SaaS Contract Stack

```
Master Subscription Agreement (MSA)
  └── Governs the overall relationship; long-form terms
  └── Typically not renegotiated per deal

Order Form
  └── Commercial terms: products, pricing, quantity, term
  └── Incorporates MSA by reference
  └── Signed per deal

Statement of Work (SOW) [if professional services]
  └── Scope, deliverables, milestones, fees
  └── Incorporates MSA by reference

Service Level Agreement (SLA)
  └── Uptime commitments, response times, remedies
  └── May be exhibit to MSA or standalone

Data Processing Agreement (DPA)
  └── Required for GDPR; describes data processing relationship
  └── Controller vs. Processor roles; sub-processor list
```

### Key Contract Provisions

**Limitation of Liability (LoL)**:
- Mutual cap on indirect/consequential damages (lost profits, lost data)
- Direct damages cap: typically 12 months of fees paid
- Carve-outs from cap: IP indemnification, confidentiality breach, gross negligence/willful misconduct, death/personal injury
- Negotiate: SaaS buyers try to remove LoL for data breach; sellers resist

**Indemnification**:
- **IP indemnification**: Seller indemnifies buyer if software infringes third-party IP. Must defend and hold harmless. Carve-outs: buyer's modifications, buyer's use in combination with other software
- **Mutual indemnification**: Each party indemnifies the other for their own negligence/misconduct

**IP Ownership in SaaS**:
- SaaS: Customer data remains customer's; SaaS provider has license to process
- Seller retains ownership of software, platform, improvements
- Watch: "work-for-hire" clauses in SOWs that give customer ownership of customizations built for them
- Customer data not to be used to train ML models without explicit consent (privacy law + commercial practice)

**Auto-Renewal (Evergreen) Clauses**:
- Typical: Contract auto-renews for additional 1-year terms unless canceled 30-90 days prior
- Notice requirement must be calendared
- Enterprise customers increasingly negotiate "annual confirmation" instead of auto-renew

**Data Residency**:
- EU customers: GDPR requires data to stay in EU or countries with adequacy decisions
- US FedRAMP: Data must stay in US government clouds
- Specify in DPA: primary region, allowed sub-processors, cross-border transfer mechanism

### SLA Design

| Tier | Uptime | Response Time | Remedy |
|---|---|---|---|
| Enterprise | 99.9% (8.7 hrs downtime/year) | P1: 1 hour | Service credits: 10% of monthly fee |
| Business | 99.5% (43.8 hrs downtime/year) | P1: 4 hours | Service credits: 5% |
| Starter | 99.0% | Best effort | No contractual remedy |

**Exclusions from SLA**: Scheduled maintenance (pre-notified), customer-caused outages, force majeure, third-party dependency failures.

---

## 3. Privacy Law

### GDPR (EU General Data Protection Regulation)

**Six Lawful Bases for Processing**:
1. Consent (freely given, specific, informed, unambiguous)
2. Contract performance
3. Legal obligation
4. Vital interests
5. Public task
6. **Legitimate interests** (most used by B2B; must conduct LIA)

**Key Rights**:
- **Art. 15**: Right of access (respond within 30 days)
- **Art. 17**: Right to erasure ("right to be forgotten")
- **Art. 20**: Right to data portability (machine-readable format)
- **Art. 21**: Right to object to processing

**Breach Notification**:
- **72-hour rule**: Notify supervisory authority within 72 hours of becoming aware of breach
- **Undue delay**: Notify data subjects "without undue delay" if high risk to their rights
- Document all breaches (even those not notifiable) in internal breach register

**Cross-Border Transfers** (EU → US):
- **EU-US Data Privacy Framework (DPF)**: Adequacy decision (July 2023); self-certify with US Dept. of Commerce. Can transfer freely.
- **Standard Contractual Clauses (SCCs)**: 2021 versions (controller-to-controller, controller-to-processor). Most commonly used alongside or instead of DPF.
- **Binding Corporate Rules (BCRs)**: For large intra-group transfers; expensive to establish.
- **Adequacy decisions**: Switzerland, UK (UK GDPR), Japan, South Korea, Israel, and others.

### CCPA / CPRA (California)

**Consumer Rights**:
- Right to know what data is collected
- Right to delete (with exceptions for legal, security, internal use)
- Right to opt out of sale or sharing of personal information
- Right to correct
- Right to limit use/disclosure of sensitive personal information

**"Sharing" definition expanded**: CPRA added sharing for cross-context behavioral advertising (not just "sale"). This captures most ad-tech data flows.

**2026 CPRA Updates (in force)**:
- **Privacy Risk Assessments**: Mandatory for high-risk processing (profiling, sensitive data, minors' data)
- **Cybersecurity Audits**: Annual audits for businesses meeting revenue/data thresholds
- Enforcement: California Privacy Protection Agency (CPPA) now primary enforcer

**Thresholds** (as of 2023):
- >$25M annual gross revenue; OR
- Processes >100,000 CA consumers/households/devices; OR
- Derives >50% of annual revenue from selling CA consumer personal information

### US State Privacy Patchwork (20+ laws, 2025)

| State | Law | Effective | Key Distinction |
|---|---|---|---|
| California | CCPA/CPRA | Jan 2020 / Jan 2023 | Most comprehensive; CPPA enforcement |
| Virginia | VCDPA | Jan 2023 | Controller-focused; no private right of action |
| Colorado | CPA | July 2023 | Universal opt-out mechanism required |
| Connecticut | CTDPA | July 2023 | Broad scope; universal opt-out |
| Texas | TDPSA | July 2024 | No revenue threshold; covers more businesses |
| Florida | FDBR | July 2024 | Revenue threshold $1B; large businesses only |
| Oregon | OCPA | July 2024 | Strong consumer rights; biometric data focus |
| Montana | MCDPA | Oct 2024 | Rural business exemptions |
| Delaware | DPDPA | Jan 2025 | Strong; nonprofit exemptions limited |
| New Hampshire | NHPA | Jan 2025 | Similar to Virginia framework |
| Maryland | MODPA | Oct 2025 | Strictest data minimization requirements |
| Minnesota | MHMDPA | July 2025 | Broad; covers more businesses than most |
| ... | ... | 2025-2026 | 8+ more states enacted; check current status |

**Compliance Strategy**: Build for California CPRA + strict states → covers most requirements nationwide. Use Consent Management Platform (OneTrust, TrustArc, Osano) for cookie consent and rights request management.

### HIPAA (Health Insurance Portability and Accountability Act)

**Who is covered**: Covered Entities (healthcare providers, health plans, clearinghouses) + Business Associates (vendors with access to PHI).

**Key Rules**:
- **Privacy Rule**: Patients control their PHI; minimum necessary standard
- **Security Rule**: Administrative, physical, technical safeguards for ePHI
- **Breach Notification Rule**: Notify HHS + individuals within 60 days; media if >500 in state

**Business Associate Agreement (BAA)**: Must be in place before BA touches PHI. Every cloud provider touching PHI needs a signed BAA (AWS, Google Cloud, Azure all offer BAAs).

**HIPAA is not a certification**: It's compliance. No certificate exists from HHS. Third-party HIPAA compliance assessment (HITRUST CSF) serves as evidence of compliance.

### COPPA (Children's Online Privacy Protection Act) — 2025 Amendments

Effective **April 22, 2026** (FTC final rule published December 2024):

**Key changes from original 1998 rule**:
- Expanded definition of "personal information" — now explicitly includes geolocation, photos, videos, audio
- Prohibits **targeted advertising** to children under 13 (new prohibition)
- Prohibits **nudge techniques** that encourage children to share more data
- Expanded **COPPA Safe Harbor** requirements for self-regulatory programs
- Schools as agents: School's consent replaces parental consent for EdTech (codified)
- Mixed-audience sites: Must conduct age screening or treat all users as children

**Age 13-17 (teens) protections**: FTC guidance (not yet law) suggests expanded protections coming for teens.

---

## 4. AI Regulation

### EU AI Act (Regulation 2024/1689) — Enforcement Timeline

| Date | Event |
|---|---|
| Aug 1, 2024 | Act entered into force |
| Feb 2, 2025 | Prohibited AI systems banned (real-time biometric surveillance, social scoring, subliminal manipulation) |
| **Aug 2, 2025** | **GPAI (General Purpose AI) model obligations apply** (transparency, copyright compliance) |
| Feb 2, 2026 | Obligations for high-risk AI in Annex I (existing regulated products) |
| Aug 2, 2026 | High-risk AI obligations for Annex III systems (biometrics, critical infrastructure, employment, etc.) |
| **Dec 31, 2027** | Full enforcement for all remaining high-risk AI systems |

**Four-Tier Risk Classification**:

| Risk Level | Examples | Requirements |
|---|---|---|
| **Unacceptable** | Social scoring, biometric mass surveillance, subliminal manipulation | **Prohibited** |
| **High** | AI in hiring, credit scoring, critical infrastructure, medical devices, law enforcement | Conformity assessment, human oversight, transparency, registration in EU database |
| **Limited** | Chatbots, emotion recognition, deepfakes | Transparency obligation (disclose AI interaction) |
| **Minimal** | Spam filters, AI in video games, recommendation systems | No specific obligations |

**GPAI (General Purpose AI) Models — Aug 2, 2025 obligations**:
- Technical documentation requirement (model architecture, training process, data sources)
- Copyright compliance policy (must honor rightsholders' opt-outs per EU DSM Directive)
- Transparency to downstream deployers
- **High-capability GPAI** (systemic risk threshold: 10²⁵ FLOPs): Additional adversarial testing, incident reporting, cybersecurity measures

**Penalties**: Up to €35M or 7% of global annual turnover (whichever higher) for violations of prohibited uses.

### NIST AI Risk Management Framework (AI RMF 1.0, 2023)

Four core functions:
1. **Govern**: Culture, policies, roles, responsibilities
2. **Map**: Categorize risks; identify AI system context
3. **Measure**: Quantify and track risks; testing and evaluation
4. **Manage**: Prioritize, respond to, and monitor risks

Not legally binding but increasingly referenced in US government procurement and becoming de facto standard.

---

## 5. Employment Law

### W-2 Employee vs. 1099 Contractor Classification

**IRS 3-Category Test** (common law factors):
1. **Behavioral control**: Does company control how worker does the job?
2. **Financial control**: Does company control business aspects (investment, profit/loss risk, payment method)?
3. **Type of relationship**: Written contracts, employee benefits, permanency, integral to business?

**California ABC Test** (Dynamex, codified AB5 2019):
- Worker is employee unless ALL THREE:
  A. Free from company control/direction in performing work
  B. Work is outside usual course of company's business
  C. Worker has established independent business in that trade/occupation

**Risk of Misclassification**:
- Back taxes (payroll taxes, withholding)
- Back benefits (workers' comp, unemployment insurance)
- Wage claims (overtime, meal breaks under California law)
- Class action exposure

**Safe Practices**:
- Short-term, project-based engagements → contractor more defensible
- Long-term, core-business, company-controlled → hire as employee
- Use PEO or EOR (Employer of Record) for international "contractors" who are actually employees

### Non-Compete Landscape (Post-FTC Rulemaking)

**FTC Rule (April 2024)**: Banned most non-competes for workers — struck down by Fifth Circuit (Ryan LLC v. FTC, August 2024). Rule not in effect; appeals ongoing.

**State Law** (governs absent federal law):
- **California**: Complete ban on non-competes (since 1872; SB 699 2024 strengthens)
- **Minnesota, North Dakota, Oklahoma**: Full bans
- **New York**: Near-ban for most workers (2024 legislation)
- **Most other states**: Enforceable if reasonable in scope, geography, duration; "blue penciling" allowed in some states

**Trade secret protection is the alternative**: Even without non-competes, DTSA allows injunctions against use of trade secrets.

**Garden leave**: Pay employee during non-compete period; more enforceable; rare in US but common in UK/EU.

---

## 6. Securities Law for Startups

### Reg D Exemptions (Private Placement)

**Rule 506(b)**:
- No general solicitation or advertising
- Up to 35 non-accredited, sophisticated investors
- Unlimited accredited investors
- Most common for angel rounds and VC rounds

**Rule 506(c)**:
- General solicitation and advertising allowed
- ALL investors must be accredited AND verified (not just self-certified)
- Takes additional diligence (tax returns, bank statements, attorney letter)
- Used for syndicate deals, AngelList, some publicly advertised rounds

**Accredited Investor Definition** (SEC 2020 expansion):
- Net worth >$1M (excluding primary residence) or income >$200K ($300K joint) last 2 years
- Knowledgeable employees of a private fund
- Holders of certain FINRA licenses (Series 7, 65, 82)
- Qualified institutional buyers (QIBs)

### SAFE (Simple Agreement for Future Equity)

Y Combinator's post-money SAFE is the market standard for pre-seed and seed rounds.

**Key Terms**:
- **Valuation Cap**: Maximum valuation at which SAFE converts (protects investor from high next-round valuation)
- **Discount Rate**: Investor gets shares at X% discount to next round price (typical: 10-20%)
- **Most Favored Nation (MFN)**: If company issues later SAFEs with better terms, this SAFE gets same terms
- **Pro Rata Right**: Investor can maintain ownership percentage in future rounds

**Post-Money SAFE Mechanics**:
```
Company raises $500K on post-money SAFE with $5M cap.
SAFE ownership = $500K / $5M = 10% (immediately calculable)

At Series A priced at $10M pre-money:
Conversion price = lower of:
  - Cap price: $5M / post-money cap shares outstanding
  - Series A price × (1 - discount)
```

**SAFE to Priced Round Conversion**: SAFEs convert to preferred stock (same class as Series A) at the cap or discounted price, whichever is lower for the investor.

---

## 7. Data Breach Notification

### US Patchwork (50-State + Federal)

**No single federal law**: 50 states + DC, Puerto Rico, Guam have their own breach notification laws.

**Common elements across states**:
- Notice to affected individuals (usually within 30-90 days)
- Notice to state AG (in many states)
- Defined trigger: acquisition of unencrypted personal information

**California SB 446 (effective 2025 — dual-track)**:
- **Track 1**: Notify within 15 days if breach involves specified sensitive data (SSN, financial, medical, credentials)
- **Track 2**: Notify within 30 days for other personal information
- Expands covered information to include citizenship status, immigration status

**Federal breach notification requirements**:
- **HIPAA**: 60 days to notify individuals + HHS; media if >500 in state
- **FTC Safeguards Rule** (financial institutions): 30 days to notify FTC; no individual notice required
- **SEC Cybersecurity Rule** (public companies): 4 business days to disclose material cybersecurity incidents on Form 8-K
- **GLBA**: Financial institutions subject to state AG notification

### Breach Response Playbook

```
Hour 0-4: Containment
  - Isolate affected systems
  - Preserve forensic evidence (do not wipe)
  - Engage incident response retainer (pre-contract before breach)
  - Brief legal counsel (attorney-client privilege protects investigation)
  - Brief executive team

Day 1-3: Assessment
  - Forensic investigation (what data, whose data, how accessed)
  - Determine if "personal information" was accessed (triggers notification)
  - State AG notification deadlines begin running

Day 3-14: Notification (varies by state law)
  - Individual notification (email, mail, or substitute notice)
  - State AG notification
  - Credit monitoring offer if financial/SSN data

Ongoing: Remediation
  - Root cause fix
  - Update security program
  - Regulatory response
  - Litigation hold
```

---

## 8. Terms of Service & Privacy Policy

### Terms of Service — Required Elements

1. **Acceptance mechanism**: "By using..." or explicit checkbox (affirmative assent preferred for enforceability)
2. **License grant**: Limited, non-exclusive, non-transferable license to use the service
3. **Acceptable use policy**: What users cannot do (illegal activity, abuse, scraping)
4. **Intellectual property**: You own your content; we own the platform
5. **Payment terms**: Billing, refunds, auto-renewal with notice
6. **Termination**: Your right to terminate their account; their right to cancel
7. **Disclaimers**: No warranties; service provided "as is"
8. **Limitation of liability**: Cap on damages; exclusion of consequential damages
9. **Indemnification**: User indemnifies you for their content/misuse
10. **Governing law and dispute resolution**: Jurisdiction; arbitration clause; class action waiver
11. **Changes to ToS**: How you'll notify of changes; continued use = acceptance

### Arbitration Clauses & Class Action Waivers

**Enforceability**: Generally enforceable under FAA (Federal Arbitration Act) per AT&T Mobility v. Concepcion (2011) and Epic Systems v. Lewis (2018).

**California restrictions**: McGill rule (9th Cir.): Cannot arbitrate public injunctive relief. PAGA claims in California: recent Supreme Court cases complicate arbitrability.

**Consumer best practices** (to maximize enforceability):
- Clickwrap (explicit "I Agree" button) vs. browsewrap (link in footer) — clickwrap required
- Email notice of changes + grace period
- Opt-out period for arbitration (often 30 days) — not legally required but strengthens enforceability

---

## 9. Platform Liability

### Section 230 (47 U.S.C. § 230)

> "No provider or user of an interactive computer service shall be treated as the publisher or speaker of any information provided by another information content provider."

**Protections**: Broad immunity from liability for third-party content. ISPs, social media, forums, review sites, AI companies (in some contexts).

**Threats to Section 230**:
- Gonzalez v. Google (2023): SCOTUS declined to narrow § 230 on technical grounds; question left open
- **TAKE IT DOWN Act (2025)**: Signed by President Trump. Platforms must remove non-consensual intimate imagery (NCII) within 48 hours of notice. Creates exception to § 230 for NCII.
- **FOSTA-SESTA (2018)**: Exception for sex trafficking content

**Does NOT protect**: Your own content; deceptive ads you knowingly ran; federal criminal liability; intellectual property claims (separate DMCA process).

### EU Digital Services Act (DSA) / Digital Markets Act (DMA)

**DSA** (live Aug 2023):
- Very Large Online Platforms (VLOPs) and Search Engines (>45M EU users): Annual risk assessments; independent audit; researcher data access; algorithmic transparency
- All platforms: Notice & action for illegal content; transparency on advertising; prohibition on targeted ads to minors

**DMA** (live March 2024):
- Gatekeepers (Apple, Google, Meta, Amazon, Microsoft, TikTok/ByteDance, Booking.com): Must enable interoperability, third-party app stores, data portability; cannot self-preference

---

## 10. M&A Legal Process

### Due Diligence Checklist (Sell-Side)

**Corporate**:
- [ ] Cap table (authorized, issued, options, warrants, SAFEs outstanding)
- [ ] Certificate of Incorporation + all amendments
- [ ] Board/stockholder minutes (complete)
- [ ] Stockholder agreements, voting agreements, right of first refusal

**IP**:
- [ ] All IP assignment agreements (employees + contractors)
- [ ] Patent portfolio (granted, pending, provisionals)
- [ ] OSS license audit report
- [ ] Trade secret protection measures documentation

**Commercial**:
- [ ] Top 20 customer contracts (MSAs, Order Forms)
- [ ] Revenue by contract (ARR/MRR, renewal dates)
- [ ] Data processing agreements
- [ ] Vendor contracts >$50K/year

**Employment**:
- [ ] Employment agreements (offer letters, PIIAs)
- [ ] Equity agreements (option grants, 409A valuations)
- [ ] Independent contractor agreements
- [ ] HR policies (handbook, anti-harassment, leave policies)

**Legal/Litigation**:
- [ ] All outstanding litigation (claims, threatened claims)
- [ ] Demand letters received
- [ ] Regulatory correspondence

**Financial**:
- [ ] 3 years audited financials (or GAAP financials)
- [ ] Deferred revenue schedule
- [ ] R&D credit documentation

### Representations & Warranties Insurance (R&W Insurance)

- Buyer-side R&W policy: insurer pays buyer if seller's reps prove false
- Reduces escrow requirements (from 10-15% to 1-2% of deal value)
- Typical for deals >$50M; increasingly common down to $20M
- Cost: 2-4% of coverage amount (one-time premium)
- Retention: typically 1% of enterprise value

### Post-Closing Covenants

- **Earnout**: Portion of purchase price contingent on hitting post-close milestones. Common in acqui-hires and when buyer/seller disagree on valuation. Key negotiation: milestone definitions, accounting, non-interference, dispute resolution.
- **Non-compete / non-solicit**: Founders typically agree to 2-year non-compete with buyer as condition of deal.
- **Transition services**: Seller provides services (IT, HR) for defined period post-close.
