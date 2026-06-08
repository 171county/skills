---
name: biz-tech-law-expert
description: Expert-level legal guidance for technology companies covering corporate formation, data privacy (GDPR/CCPA/state laws), AI regulation (EU AI Act, US federal/state), tech contracts (SaaS MSAs), employment law (PIIA, non-competes, AB5), platform law (Section 230, DMCA), cybersecurity law (SEC rules), antitrust (DOJ v. Google, FTC v. Meta), international law (UK GDPR, China PIPL, export controls), and fundraising regulations (Reg D, Reg CF, Reg A+). Current as of mid-2026. For educational/informational purposes; not legal advice.
---

# Business Technology Law Expert

This skill provides practitioner-grade legal analysis for technology companies. All content is educational and informational — not legal advice. Always recommend consulting qualified counsel for specific transactions or compliance decisions.

---

## SECTION 1: TECH COMPANY FORMATION & GOVERNANCE

### Why Delaware C-Corp?

The Delaware C-Corporation is the near-universal choice for venture-backed tech startups because:
- Delaware's Court of Chancery provides specialized, predictable corporate jurisprudence
- NVCA model documents are drafted for Delaware law
- Institutional investors typically require Delaware C-Corp as a condition of investment
- Delaware's General Corporation Law (DGCL) provides maximum flexibility for capital structure
- S-Corp status (limited to 100 shareholders, single class of stock) is incompatible with VC preferred stock

**Formation documents**: Certificate of Incorporation (filed with Delaware Secretary of State), Bylaws (internal governance), Stock Option Plan (equity incentives), Action by Written Consent of Incorporator.

### NVCA Model Documents (Updated Through October 2025)

The National Venture Capital Association (NVCA) publishes the industry-standard model documents used in US venture financings. The suite includes:

1. **Term Sheet** — Non-binding summary of deal economics and governance
2. **Certificate of Incorporation (COI)** — Creates preferred stock series; defines liquidation preferences, conversion rights, protective provisions, anti-dilution
3. **Preferred Stock Purchase Agreement (SPA)** — Sets out the investment terms, representations and warranties, conditions to closing
4. **Investors' Rights Agreement (IRA)** — Registration rights, information rights, right of first offer (ROFO) on future rounds, lock-up obligations
5. **Voting Agreement** — Board composition, drag-along rights, founder voting obligations
6. **Right of First Refusal and Co-Sale Agreement (ROFR/Co-Sale)** — Transfer restrictions on founder/stockholder shares

**Key 2025 NVCA Update**: Default language was added providing that rights to designate a board member do not transfer to an assignee of the shares (preventing governance rights from transferring in secondary transactions).

### Key Governance Provisions

**Board Composition** (typical Series A):
- 2 common seats (founders)
- 2 preferred seats (lead investors)
- 1 independent/mutual consent seat
- Protective provisions give preferred stockholders veto rights over: new stock issuances, asset sales, amendments to COI, changes to board size, liquidation/dissolution, incurring indebtedness above threshold

**Liquidation Preferences**:
- Standard is 1x non-participating (98% of venture rounds in Q2 2025 used this structure)
- Participating preferred allows investors to receive liquidation preference AND convert to common — far more dilutive to founders
- Waterfall: preferred before common; senior series before junior series

**Anti-Dilution Provisions**:
- Broad-based weighted average (BBWA) — market standard; less punitive to founders
- Narrow-based weighted average — more protective to investors
- Full ratchet — extremely investor-friendly; rare in current market; devastating in down rounds
- Anti-dilution adjusts conversion price of preferred to common upon issuance of shares at a lower price

**Drag-Along Rights** (Voting Agreement):
- Require founders and other stockholders to vote in favor of an approved sale if a specified threshold of preferred (e.g., majority or Series A majority) approves
- Designed to prevent minority stockholders from blocking acquisitions
- Key negotiation points: minimum sale price triggers, who constitutes the approving group, founder carve-outs

**Co-Sale Rights (Tag-Along)**:
- Give investors the right to sell a pro-rata portion of their shares alongside founders in secondary sales
- Prevents founders from cashing out while investors remain locked in
- Right of First Refusal (ROFR): company gets first right to purchase shares before a third party; investors get second right if company declines

**Information and Inspection Rights**:
- Major investors (typically investors above a specified threshold, e.g., $500K–$2M) receive: audited annual financials, monthly/quarterly unaudited financials, annual budget/business plan
- Board observer rights often granted to smaller institutional investors
- Confidential treatment; typically terminates at IPO

**Fiduciary Duties**:
- Directors owe duty of care (informed decision-making) and duty of loyalty (no self-dealing) to the corporation
- Preferred-designated directors in conflict situations: Delaware courts have recognized tension between director duties to the corporation and loyalties to the fund
- Business Judgment Rule protects directors who act in good faith on informed basis
- Entire fairness review applies to self-interested transactions
- Delaware courts have held that preferred stockholders' contractual rights are governed by the certificate of incorporation, not fiduciary duty principles (Trados/MFW line of cases)

---

## SECTION 2: DATA PRIVACY LAW

### GDPR (EU General Data Protection Regulation)

**Scope**: Applies to any company processing personal data of EU residents, regardless of where the company is located.

**Six Lawful Bases** (Article 6):
1. Consent — must be freely given, specific, informed, unambiguous; separate from ToS
2. Contract performance
3. Legal obligation
4. Vital interests
5. Public task
6. Legitimate interests — requires balancing test; most commonly used by tech companies but subject to challenge

**Key Individual Rights**:
- Article 15: Right of access
- Article 16: Right to rectification
- Article 17: Right to erasure ("right to be forgotten") — applies where data no longer necessary for purpose, consent withdrawn, or no legitimate grounds to continue processing
- Article 18: Right to restriction of processing
- Article 20: Right to data portability
- Article 21: Right to object (especially to direct marketing and legitimate-interest processing)
- Article 22: Right not to be subject to solely automated decisions with legal/significant effects

**Data Processing Agreements (DPAs)** (Article 28):
- Required when controller engages a processor
- Must specify: subject matter, duration, nature and purpose, type of personal data, obligations and rights of controller
- Processor must act only on controller's documented instructions
- Sub-processor restrictions; notification of sub-processors

**Standard Contractual Clauses (SCCs)**:
- 2021 modernized SCCs with modular structure (four modules: C2C, C2P, P2C, P2P)
- Required for transfers from EEA to countries without adequacy decisions
- September 2024: European Commission launched consultation on new module for transfers where both parties are subject to GDPR
- Transfer Impact Assessment (TIA) required alongside SCCs post-Schrems II
- EU-US Data Privacy Framework (DPF) in effect as of 2023; provides adequacy for certified US companies — monitor for Schrems III challenge

**Penalties**: Up to €20M or 4% of global annual turnover, whichever is higher.

### CCPA/CPRA (California)

**Current Law (post-CPRA 2023)**:
- Applies to for-profit businesses that: (a) have $25M+ gross annual revenues; OR (b) buy/sell/share personal information of 100,000+ consumers/households; OR (c) derive 50%+ of annual revenues from selling personal information
- California Privacy Protection Agency (CPPA) is dedicated enforcement authority (first US state privacy regulator)
- Enforcement began February 2024; CPPA issued $100M+ in enforcement actions in 2024

**Consumer Rights**:
- Right to know / access
- Right to delete
- Right to correct
- Right to opt out of sale/sharing of personal information
- Right to opt out of automated decision-making in significant decisions
- Right to non-discrimination for exercising rights
- Right to limit use of sensitive personal information

**Sensitive Personal Information**: SSNs, financial account data, precise geolocation, racial/ethnic origin, religious beliefs, union membership, genetic/biometric data, health data, sexual orientation/gender identity, private communications, immigration status

**Automated Decision-Making (ADM)**: CPPA rules require opt-out rights when AI/algorithms make or substantially replace human decision-making in significant decisions (employment, education, finance, housing, healthcare). Risk assessment requirements for ADM.

**Key Difference from GDPR**: CCPA/CPRA uses opt-out model (processing is lawful until consumer opts out); GDPR requires opt-in consent for sensitive data.

### US State Privacy Law Landscape (as of mid-2026)

20+ states have enacted comprehensive consumer data privacy laws. Most share: privacy notice, opt-out for data sale/targeted advertising, consumer access/delete rights, data protection assessments.

**Currently in Effect (selected)**:
- California (CCPA/CPRA) — enforcement since 2020/2024
- Virginia (VCDPA) — effective January 1, 2023
- Colorado (CPA) — effective July 1, 2023
- Connecticut (CTDPA) — effective July 1, 2023
- Texas (TDPSA) — effective July 1, 2024
- Florida (FDBR) — effective July 1, 2024 (applies only to large controllers: $1B+ revenue)
- New Jersey, Delaware, Nebraska, New Hampshire — effective January 1, 2025
- Indiana — effective January 1, 2026
- Maryland (MODPA) — notable for broad data minimization and prohibition on selling sensitive data
- Minnesota, Rhode Island — effective 2025–2026

**Key Compliance Thresholds (most states)**:
- Process personal data of 100,000+ consumers; OR
- Process personal data of 25,000+ consumers AND derive 25%+ revenue from data sales

**Privacy Notices Must Include** (common across states):
- Categories of personal data collected
- Purposes for processing
- How consumers exercise rights
- Categories shared with third parties
- Whether data is sold or used for targeted advertising
- How to opt out of sale/targeted advertising

### HIPAA (Health Tech)

- Applies to covered entities (health plans, providers, clearinghouses) and their business associates
- Protected Health Information (PHI): health information tied to individual identity
- Business Associate Agreements (BAAs) required for vendors processing PHI
- Security Rule: administrative, physical, and technical safeguards
- Breach Notification Rule: notice to affected individuals within 60 days; HHS notification; media notice if 500+ individuals in a state
- Tech startups using health data: determine if HIPAA-covered; if in doubt, implement safeguards and sign BAAs
- FTC has increasingly applied FTC Act Section 5 to health apps not covered by HIPAA

### COPPA (Children's Apps)

- Applies to websites/online services directed to children under 13, or with actual knowledge of users under 13
- Requires verifiable parental consent before collecting personal information from children
- Privacy notice requirements
- Right to delete children's data
- FTC enforcement: substantial civil penalties
- COPPA 2.0 legislation has been repeatedly proposed but not enacted as of mid-2026

---

## SECTION 3: AI-SPECIFIC REGULATIONS (2024–2026)

### EU AI Act

**Entered into force**: August 1, 2024
**Full application**: August 2, 2026
**Prohibited AI practices applied**: February 2, 2025
**GPAI model obligations applied**: August 2, 2025

**Risk Tiers**:

**1. Unacceptable Risk (Prohibited)**:
- Subliminal manipulation systems
- Exploitation of vulnerable groups (disability, age)
- Social scoring by public authorities
- Real-time remote biometric identification in public spaces (with limited law enforcement exceptions)
- Emotion recognition in workplace/education
- Biometric categorization to infer sensitive characteristics
- Predictive policing based solely on profiling

**2. High-Risk AI Systems** (Annex III — requires conformity assessment, registration, ongoing obligations):
- Critical infrastructure (energy, water, transport)
- Education (student assessment, access decisions)
- Employment (recruitment, HR management, performance evaluation)
- Essential private services (credit scoring, insurance risk assessment)
- Law enforcement (polygraphs, crime risk assessment)
- Migration, asylum, and border control
- Administration of justice
- Safety components of products (medical devices, vehicles)

**High-Risk Provider Obligations**:
- Risk management system throughout lifecycle
- Data governance and quality measures
- Technical documentation
- Record-keeping/logging
- Transparency and information to users
- Human oversight measures
- Accuracy, robustness, cybersecurity requirements
- EU Declaration of Conformity
- CE marking and registration in EU database

**3. Limited-Risk AI**:
- Transparency obligations only
- Chatbots must disclose AI nature to users
- Deepfakes must be labeled as AI-generated
- AI-generated content must be machine-readable labeled

**4. Minimal/No Risk**:
- Spam filters, AI in video games, etc.
- No mandatory obligations under the AI Act

**General Purpose AI (GPAI) Models** (applies from August 2, 2025):
- All GPAI providers: transparency obligations, technical documentation, copyright compliance
- High-capability models (training compute > 10^25 FLOPs): systemic risk obligations including model evaluation, adversarial testing, incident reporting to the Commission

**Penalties**:
- Prohibited AI violations: up to €35M or 7% of global annual turnover
- High-risk violations: up to €15M or 3% of global annual turnover
- Providing incorrect information: up to €7.5M or 1.5% of global annual turnover

### US Federal AI Regulatory Landscape

**Biden EO 14110 (October 2023)**: Required safety evaluations for frontier AI models, safety reporting, watermarking AI-generated content, equity impact assessments. **Revoked January 20, 2025** on Trump's first day — his EO "Removing Barriers to American Leadership in Artificial Intelligence" replaced it with a pro-innovation, deregulatory framework.

**Trump AI EO (January 2025)**: Frames AI as national competitiveness issue; directs agencies to reduce regulatory burden on AI; directs NIST and others to develop voluntary AI standards aligned with innovation. Signal: federal government will not impose significant AI-specific regulations; states are filling the void.

**NIST AI Risk Management Framework (AI RMF 1.0)**: Four functions — Govern, Map, Measure, Manage. Increasingly referenced in state laws (Colorado AI Act), federal contracts, and enterprise procurement requirements. Not legally binding at federal level but has significant operational weight.

**EEOC AI Hiring Guidance**: 
- May 2023 guidance on AI/automated systems in hiring under Title VII removed from EEOC website January 27, 2025 under Trump administration
- September 2025: EEOC released new guidance maintaining that disparate impact liability applies to AI hiring tools; employers remain liable if AI tools produce discriminatory outcomes regardless of intent
- Core principle: Title VII, ADA, ADEA still apply to AI-assisted hiring; employers must validate AI tools do not have adverse impact on protected classes

### US State AI Laws (as of mid-2026)

**Colorado AI Act (SB 24-205, originally signed May 2024)**:
- Originally effective February 1, 2026: first comprehensive US state AI law; required annual impact assessments, risk management programs aligned with NIST AI RMF, algorithmic discrimination duties
- **MAJOR UPDATE**: Colorado enacted replacement law SB 26-189 (signed May 14, 2026), significantly scaling back the original. The replacement dropped risk management programs and annual impact assessments in favor of a narrower notice-and-transparency framework
- Applies to developers and deployers of "high-risk AI" (consequential decisions affecting employment, housing, education, healthcare, financial services, legal services)

**Texas Responsible AI Governance Act (TRAIGA, signed June 22, 2025)**:
- Effective January 1, 2026
- Prohibits AI used to: manipulate human behavior, assign social scores (government only), discriminate unlawfully, infringe constitutional rights, capture biometric data without consent
- Disparate impact alone does not establish a violation
- Enforcement by Texas AG only; no private right of action; 60-day cure period

**California**:
- SB 1047 (Frontier AI safety bill): vetoed by Governor Newsom in September 2024
- AB 2013 (AI training data transparency): requires disclosure of training data for AI systems
- SB 53 (signed September 29, 2025): targets developers of frontier AI models; requires incident reporting and safety commitments
- California Civil Rights Council finalized ADS employment regulations effective October 1, 2025: using automated decision systems (ADS) in employment can violate state anti-discrimination laws if disparate impact on protected characteristics

**Illinois (HB 3773, effective January 1, 2026)**:
- Employers cannot use AI in ways that result in bias against protected classes
- Must notify employees/candidates when AI used in employment decisions

**New Jersey**:
- Division on Civil Rights regulations (December 2025): prohibit AI tools in hiring with disparate impact on protected characteristics under NJ Law Against Discrimination

---

## SECTION 4: TECH CONTRACTS (SaaS MSAs & ENTERPRISE AGREEMENTS)

### SaaS Master Service Agreement (MSA) Structure

A well-drafted SaaS MSA includes: Master Agreement (framework terms) + Order Form(s) (specific subscription details) + exhibits (DPA, SLA, acceptable use policy).

**The Five Most-Negotiated Clauses**:

**1. Limitation of Liability**
- Standard structure: mutual exclusion of indirect/consequential/incidental/special damages; aggregate cap on direct damages
- Typical cap: fees paid in the 12 months preceding the claim (providers prefer this); 12-24 months' fees (enterprise customers often push for higher)
- **Carve-outs from cap** (items not subject to limitation): IP indemnification, data breach/confidentiality breaches, willful misconduct/gross negligence, death/personal injury
- Provider negotiation: define consequential damages exclusion broadly; push for low aggregate cap tied to subscription fees
- Customer negotiation: push for higher cap (12-24 months minimum); carve out data breaches and confidentiality violations from cap

**2. Indemnification**
- Standard provider IP indemnification: provider defends and indemnifies customer against third-party IP infringement claims arising from use of the service as-contracted
- Provider indemnification does NOT typically cover: (a) customer modifications, (b) combination with non-provider products, (c) use outside documented specifications
- Typical customer indemnification: customer data and content, customer's breach of agreement, customer's use in violation of law
- Mutual indemnification for gross negligence/willful misconduct common in enterprise deals

**3. IP Ownership**
- Background IP: each party retains ownership of pre-existing intellectual property
- Customer data: customer retains all rights to its data; provider gets limited license to operate the service
- Foreground IP/Work Product: if provider creates custom code for customer, determine ownership upfront (assignment vs. license)
- Aggregate/Anonymized Data: providers often assert right to use aggregated/anonymized usage data for product improvement — customers should limit this and require genuine anonymization standards
- AI Training Data: increasingly negotiated; customers should carve out their data from being used to train AI models that benefit other customers

**4. Service Level Agreements (SLAs)**
- Uptime commitment: enterprise typically 99.9% (allows ~8.7 hours downtime/year) or 99.95% (allows ~4.4 hours)
- Exclusions from downtime calculation: scheduled maintenance, force majeure, customer-caused outages, third-party dependencies
- Remedies for SLA breach: service credits (typically 5–30% of monthly fees for tier of outage); rarely actual damages
- SLA credits are often providers' exclusive remedy for service failures — customers should ensure SLA credits do not waive other rights for material/prolonged outages

**5. Data Handling and Security**
- GDPR/state privacy law compliance obligations
- Data processing agreement as exhibit
- Security standards: SOC 2 Type II certification, penetration testing, encryption at rest and in transit, incident notification timeline (24-72 hours is market standard)
- Data portability at termination: customer right to export data for 30-90 days post-termination
- Data deletion: provider deletes/destroys customer data within specified period after termination

**Termination Rights**:
- For convenience: customers want right to terminate without penalty on 30-90 days notice; providers resist or demand minimum commitment period
- For cause: both parties have right to terminate for material uncured breach (typically 30-day cure period)
- Effect of termination: data export window, transition assistance, survival of confidentiality/liability/IP clauses
- Auto-renewal: specify notice period to prevent unwanted renewals (typically 30-90 days before renewal date)

### API Terms of Service — Key Issues
- Rate limiting and acceptable use
- Data scraping restrictions
- Attribution requirements
- Prohibitions on using API output to train competing models
- Liability for API downtime (usually very limited)
- Unilateral modification rights (common; require notice)

---

## SECTION 5: EMPLOYMENT LAW FOR TECH STARTUPS

### Offer Letters vs. Employment Agreements
- California and most states: at-will employment; offer letter sufficient for most employees
- Employment agreements (with definite term or for-cause termination only) create legal obligations; rarely appropriate except for C-suite with specific negotiated protections
- Offer letters should reference: compensation, equity grant, start date, at-will status, PIIA requirement, and not contain any promises about job security

### Proprietary Information and Invention Assignment Agreement (PIIA)
Every tech startup employee and contractor should sign a PIIA before or at start of work. Key provisions:
- **Invention assignment**: All inventions made during employment (or related to company business) assigned to company
- **California Labor Code Section 2870 carve-out**: Inventions on employee's own time, without company resources, unrelated to company's business are NOT assigned (mandatory in CA)
- **Prior inventions**: Employee must disclose prior inventions to avoid later disputes
- **Confidentiality**: Obligation to maintain trade secrets and confidential information
- **Non-solicitation of employees/customers**: Replaces non-compete in California; enforceable in most states
- **Work for hire**: Employee content is work made for hire owned by company
- **Return of property**: All company property returned at termination
- Failure to get signed PIIAs from early employees/founders is a major diligence issue in VC rounds and M&A

### Non-Compete Agreements — State Patchwork

**Federal Level**:
- FTC final rule banning most non-competes (April 2024): struck down by N.D. Texas (August 2024) as exceeding FTC authority
- FTC voted 3-1 to vacate rule and abandon appeal (September 5, 2025)
- Non-compete rule formally removed from Code of Federal Regulations (February 12, 2026)
- FTC approach shifted to case-by-case enforcement under Section 5 of FTC Act for overbroad/coercive non-competes

**State-by-State**:
- **California**: Non-competes are void and unenforceable (Business & Professions Code §16600); includes post-employment non-solicitation of customers in many circumstances; SB 699 and AB 2424 (2024) reinforced prohibition
- **Minnesota** (effective 2023): Non-competes void for employment entered after January 1, 2023
- **Oklahoma, North Dakota**: Long-standing bans on non-competes
- **Massachusetts, Washington, Illinois**: Require consideration, reasonable geographic/time scope; salary thresholds for enforceability
- **Florida**: Highly protective of non-compete enforcement; courts apply reasonable standard; garden leave common
- **Texas, New York**: Enforced if reasonable in scope, geography, and duration

**Non-Solicitation Clauses**: Generally more enforceable than non-competes. Employee non-solicitation (preventing raiding former colleagues) widely enforced. Customer non-solicitation varies by state — California courts scrutinize closely.

### Worker Classification (Employee vs. Independent Contractor)

**California AB5 (effective January 1, 2020; ABC test)**:
Must satisfy ALL THREE to classify as independent contractor:
- (A) Free from control/direction of hiring entity
- (B) Performs work outside usual course of hiring entity's business
- (C) Customarily engaged in independently established trade/occupation
- Ninth Circuit upheld AB5 in 2024
- Numerous industry exemptions (over 100 carve-outs): licensed professionals, certain creative workers, truckers (ongoing litigation), app-based gig workers (Prop 22)
- Misclassification consequences: unpaid wages, benefits, payroll taxes, penalties

**Federal (IRS/DOL)**:
- DOL 2024 Final Rule (effective March 2024) restored multi-factor economic reality test: (1) opportunity for profit/loss, (2) investments, (3) degree of permanence, (4) nature/degree of control, (5) integral part of employer's business, (6) skill/initiative
- No single factor controls; totality of circumstances

### Equity Vesting in Employment Contracts
- Standard: 4-year vesting with 1-year cliff (25% vests at 1-year cliff; remaining 25%/year or 1/48 per month)
- Acceleration provisions: single-trigger (change of control alone triggers vesting) vs. double-trigger (change of control + termination within X months)
- Double-trigger acceleration is market standard for employees; single-trigger may be negotiated by key executives
- Early exercise and 83(b) elections: founders and early employees should file 83(b) election within 30 days of restricted stock grant to minimize ordinary income tax; missing the window is catastrophic and irrecoverable

---

## SECTION 6: PLATFORM & CONTENT LAW

### Section 230 (Communications Decency Act, 47 U.S.C. § 230)
- **Core protection**: "No provider or user of an interactive computer service shall be treated as the publisher or speaker of any information provided by another information content provider"
- Protects platforms from liability for user-generated content
- Does NOT protect: (1) platforms that materially contribute to illegality, (2) federal criminal liability, (3) FOSTA-SESTA (sex trafficking), (4) intellectual property claims

**Current Status (mid-2026)**:
- Section 230 remains law; no major federal reform enacted through mid-2026
- TAKE IT DOWN Act (signed May 19, 2025): criminalizes non-consensual intimate imagery (NCII) including AI-generated NCII; requires platforms with UGC to implement takedown procedures and remove reported NCII within 48 hours — first major carve-out from Section 230 protections
- Ongoing reform proposals (EARN IT Act, Platform Accountability and Consumer Transparency Act) have not passed
- Supreme Court: Gonzalez v. Google (2023) and Twitter v. Taamneh — Court declined to expand §230 liability; upheld platforms' broad protection

**AI-Generated Content**: Section 230 status of AI-generated content is legally unresolved. Content generated by the platform's own AI model likely does not receive §230 protection (not "provided by another information content provider"). User-prompted AI outputs present complex intermediate questions.

### DMCA Safe Harbor (17 U.S.C. § 512)

**Requirements for Safe Harbor**:
1. Designated DMCA agent registered with Copyright Office
2. Expeditious removal of infringing content upon valid takedown notice
3. No actual knowledge of infringement (or acts expeditiously upon obtaining knowledge)
4. Does not financially benefit directly from infringing activity when having ability to control it
5. Repeat infringer policy implemented and enforced

**Takedown Notice Requirements (§512(c)(3))**:
- Identification of copyrighted work claimed infringed
- Identification of allegedly infringing material with location information
- Contact information of complainant
- Good faith belief statement
- Accuracy statement under penalty of perjury
- Signature

**Counter-Notice**: User can file counter-notice disputing takedown; service must restore content within 10-14 days unless copyright holder files suit.

**AI Training Data Copyright**: Active litigation area. Authors Guild v. OpenAI, other cases: whether training AI on copyrighted works constitutes infringement. No final judicial resolution as of mid-2026.

---

## SECTION 7: CYBERSECURITY LAW

### SEC Cybersecurity Disclosure Rules (Adopted July 26, 2023)

**Applies to**: Public companies (reporting issuers)

**Incident Reporting (Form 8-K, Item 1.05)**:
- Must file within **4 business days** of determining an incident is "material" — not 4 days after discovery
- Materiality standard: information a reasonable investor would consider important to investment decision; financial impact, operational disruption, reputational harm
- If full information not available at time of 4-day deadline, file with available information and update via amendment
- National security/public safety exception: DOJ can delay disclosure for limited periods
- Smaller reporting companies had 180-day additional grace period (expired June 2024)

**Annual Report Disclosures (Form 10-K)**:
- Management's cybersecurity risk management strategy
- Board oversight of cybersecurity risk (which committee, how often, board expertise)
- Management's role and cybersecurity expertise
- Material cybersecurity incidents in past fiscal year

### State Data Breach Notification Laws
- All 50 states have data breach notification laws; vary in timing, covered data, thresholds
- Common requirements: notice to affected individuals within 30-90 days; notice to state AG; media notice if large number of affected residents
- Most expansive: California (expanded definition of personal information), New York (SHIELD Act), Illinois
- Texas: HB 4 (2023) — requires notification within 60 days; AG notification for 250+ individuals affected
- Key covered data types: SSNs, financial account numbers, driver's license numbers, medical information, login credentials

### FTC Act Section 5 (Cybersecurity)
- FTC has authority to bring enforcement actions against "unfair or deceptive acts or practices"
- Deception: companies that promise reasonable security and fail to deliver
- Unfairness: companies that fail to implement reasonable security measures, causing harm
- Consent decrees: FTC regularly settles cybersecurity cases with consent orders requiring 20-year security audit programs
- Health apps: FTC increasingly applies Section 5 to health apps that are not covered by HIPAA (FTC Health Breach Notification Rule)

### SOC 2 and Legal Implications
- SOC 2 Type II: independent audit of security, availability, processing integrity, confidentiality, and privacy controls over 6-12 month period
- Not legally required but increasingly mandatory in enterprise procurement; often required in SaaS contracts
- SOC 2 report does not guarantee security; it validates processes exist
- Legal use: evidence of reasonable security practices in breach litigation; reduces potential FTC enforcement exposure

---

## SECTION 8: ANTITRUST & COMPETITION

### DOJ v. Google (Search Antitrust)

**August 2024 Verdict**: Judge Mehta found Google violated Section 2 of Sherman Act — illegally maintained monopoly in general search services and search text advertising through exclusive default search agreements with Apple, Android OEMs, browser makers.

**September 2, 2025 Remedies Decision**:
- Court rejected DOJ's request for structural remedies (breakup, forced divestiture of Chrome browser)
- Behavioral remedies imposed: prohibit exclusive default search agreements; require Google to share search data (search queries and results) with qualified competitors; limits on exclusive contracts
- DOJ appealed on grounds that remedies are insufficient

**Implications for tech companies**: Exclusive default arrangements in distribution channels scrutinized; data-sharing as a remedy signals courts may order data access to restore competition.

### FTC v. Meta

**Trial**: April 14 – June 2025 (six-week bench trial). FTC alleged Meta illegally maintained monopoly in "personal social networking" by acquiring Instagram (2012) and WhatsApp (2014) to neutralize competitive threats.

**November 18, 2025 Verdict**: Judge Boasberg ruled in favor of Meta — FTC failed to prove Meta currently holds monopoly power in personal social networking; market definition rejected; acquisitions found lawful given conditions at time.

**January 2026**: FTC filed notice of appeal.

**Implications**: Retroactive merger challenge is difficult — courts focus on current market power, not conditions at time of acquisition. Market definition in platform/social media cases remains contested.

### HSR Filing Thresholds (2025)
- Size-of-transaction threshold: **$126.4 million** (increased from $119.5M; effective February 21, 2025)
- Reporting required when: (a) transaction size exceeds threshold AND (b) size-of-person test met
- New HSR rules (effective February 2024): significantly expanded information requirements in pre-merger notification filings; more detailed disclosure of competitive overlaps

### EU Digital Markets Act (DMA)

**Applies to "Gatekeepers"** (designated platforms meeting usage/revenue thresholds):
- Designated gatekeepers: Alphabet (Google), Apple, Meta, Microsoft, Amazon, ByteTok

**Key DMA Obligations**:
- No self-preferencing: cannot rank own services above competitors
- Interoperability: must allow third-party apps and services to interoperate
- Data portability: users can move data to competing services
- No exclusive defaults: cannot require gatekeepers' own services as default

**Enforcement (2025)**:
- Apple fined €500M for App Store steering restrictions (April 2025)
- Meta fined €200M for no-consent alternative (April 2025)
- Alphabet preliminary finding of DMA breach: Google Search self-preferencing; Google Play app store steering restrictions

### AI Competition Law
- DOJ/FTC/CMA/EC joint statement (July 2024): three AI competition concerns — concentrated control of compute/chips, incumbent digital firms entrenching AI market power, anticompetitive arrangements
- AI-specific HSR considerations: acquisitions of AI companies with small revenues but large data/talent/IP may be reviewed under nascent-competitor or buy-and-kill theories
- Algorithmic pricing coordination: emerging antitrust risk; DOJ has investigated real estate and hotel industries; AI pricing tools face scrutiny under per se price-fixing theories if algorithms share information or facilitate coordination
- EU AI Act + DMA intersection: GPAI model providers that are also DMA gatekeepers face overlapping obligations

---

## SECTION 9: INTERNATIONAL TECH LAW

### UK GDPR (Post-Brexit)

- UK retained GDPR domestically as "UK GDPR" alongside Data Protection Act 2018
- **EU adequacy decision** for UK in effect: data can flow from EU to UK without additional safeguards (subject to review)
- **International Data Transfer Agreements (IDTA)**: UK equivalent of EU SCCs for transfers from UK to non-adequate countries
- UK Information Commissioner's Office (ICO) is the supervisory authority
- UK government has pursued a more "innovation-friendly" approach (Data (Use and Access) Act 2025); maintains fundamental GDPR framework but allows more flexibility

### China PIPL (Personal Information Protection Law, effective November 2021)

**Key Requirements**:
- Consent-based model (with exceptions for contract, legal obligation, vital interests, public interest, news)
- Data localization: Critical Information Infrastructure Operators (CIIOs) and processors of "large amounts" of personal information must store data within China
- Cross-border transfer conditions: (1) Pass CAC security assessment (required for CIIOs and large-scale processors), (2) Sign standard contracts with overseas recipient, OR (3) Pass certification
- Sensitive personal information: requires specific consent; includes biometrics, religion, medical health, financial account, location tracking, minors' data
- Data minimization: collect only what is necessary
- Purpose limitation
- Individual rights (access, correction, deletion, portability)
- Penalties: up to ¥50M or 5% of prior year's revenue

**PIPL vs. GDPR Key Differences**:
| Aspect | GDPR | PIPL |
|--------|------|------|
| Philosophical basis | Individual rights, democracy | State sovereignty, social order |
| Data localization | No requirement | Yes — for CIIOs and large processors |
| Cross-border transfer | SCCs or adequacy | CAC assessment, standard contract, or certification |
| Enforcement | DPAs + courts | Cyberspace Administration of China (CAC) |
| Government access | Subject to rule of law | Broad government access rights |

**Practical implications for US companies in China**: Must appoint Chinese data protection representative; cannot transfer employee/customer data outside China without CAC compliance; US companies using Chinese cloud vendors (Alibaba Cloud, Tencent Cloud) face restrictions on where data is stored.

### Export Controls on AI (BIS/EAR)

**Background**: Bureau of Industry and Security (BIS) within US Department of Commerce administers Export Administration Regulations (EAR).

**Advanced Computing Chips Controls (October 2022, expanded October 2023, December 2024)**:
- Export controls on high-performance AI chips to China, Hong Kong, Macau
- Entity List designations for Chinese AI/semiconductor companies
- Foreign Direct Product Rule (FDPR) extended globally for advanced computing items

**AI Diffusion Rule**:
- Biden administration issued "Framework for Artificial Intelligence Diffusion" (January 15, 2025): tiered global restrictions on exports of advanced AI chips and AI model weights
- **Rescinded by Trump administration on May 13, 2025** (two days before it was to take effect); found to be "overly bureaucratic" and harmful to US innovation/diplomacy
- BIS issued replacement guidance and policy statement on advanced computing ICs; announced new replacement rule to come

**AI Model Weights Controls**:
- BIS added new EAR control on AI model weights for certain closed-weight dual-use AI models
- New due diligence requirements for companies using, granting access to, or trading in semiconductors used in AI (announced May 2025)

**Current framework (mid-2026)**:
- Pre-existing advanced chip controls from 2022/2023 remain in effect
- Entity List restrictions remain
- Heightened due diligence expectations from BIS guidance documents
- Monitor BIS for replacement rule for AI diffusion

---

## SECTION 10: FUNDRAISING LAW

### Regulation D — Private Placements

**Rule 506(b)**:
- Raise unlimited amount from accredited investors + up to 35 sophisticated non-accredited investors
- **No general solicitation or advertising** allowed
- Self-certification for accredited investors (company takes investor representation at face value)
- No SEC registration required; state "Blue Sky" preemption
- Form D must be filed with SEC within 15 days of first sale
- ~$170 billion raised under Rule 506(b) in fiscal year 2024 (vs. $12B under 506(c))

**Rule 506(c)**:
- Raise unlimited amount; general solicitation and advertising permitted
- **All investors must be verified accredited investors** (reasonable steps required; cannot self-certify)
- Verification methods: review of tax returns/W-2s, bank/brokerage statements, CPA/attorney letter, SEC-approved self-certification (recent guidance eases burden for returning investors)
- Must file Form D within 15 days of first sale

**Accredited Investor Definition (current)**:
- Income: $200K individual / $300K joint in each of the last 2 years, expected same current year
- Net worth: $1M+ excluding primary residence (alone or with spouse)
- Professional: SEC/FINRA license holders (Series 7, 65, 82), knowledgeable employees of private funds, certain family offices

### Regulation CF (Crowdfunding)

- Raises up to **$5 million** in a 12-month period from both accredited and non-accredited investors
- Must use a registered crowdfunding platform (SEC/FINRA registered)
- Investor limits apply to non-accredited investors based on income/net worth (typically $2,200–$107K max per year)
- Disclosure requirements: business description, financials (scaled by offering size), risk factors
- Financial statement requirements: certified by officer (< $107K); reviewed by CPA ($107K–$535K); audited by CPA ($535K–$5M)
- Company must be US-based; not available for Exchange Act reporting companies, investment companies, or blank check companies
- 2024 total: $343.6 million raised (down 18% from 2023's $423M)

### Regulation A+ (Mini-IPO)

- **Tier 1**: Raise up to $20M in 12 months; Blue Sky registration required
- **Tier 2**: Raise up to **$75 million** in 12 months; Blue Sky preempted; ongoing reporting obligations
- Open to all investors (not just accredited)
- Must file offering circular (Form 1-A) and obtain SEC qualification
- Tier 2: audited financials, ongoing annual/semi-annual/event reporting
- Less commonly used than Reg D; more disclosure burden

### What You Actually Negotiate in a Venture Round

**Economics (Term Sheet)**:
- Pre-money valuation and resulting dilution
- Option pool (typically 10–20% of post-money cap table; usually carved out pre-money, increasing effective dilution)
- Pro-rata/follow-on rights: right to invest proportionally in future rounds to maintain ownership
- Anti-dilution: negotiate for BBWA vs. full ratchet

**Governance**:
- Board composition and who controls independent seat
- Protective provisions (veto rights): investors push for broad list; founders limit to major events
- Drag-along threshold: higher threshold = more founder protection
- Information rights thresholds: set minimum investment threshold to receive full information rights

**Investor Rights Agreement**:
- Registration rights: S-1 demand rights (when can investors demand IPO registration), S-3 shelf rights, piggyback rights
- Lock-up: investors commit not to sell shares for period after IPO (typically 180 days)
- ROFR on future rounds: investors get right to participate in future financings

**Legal Process**:
- After term sheet: full legal documentation takes 4–8 weeks
- Representations and warranties: company and founders make extensive representations; breaches can trigger indemnification
- Form D filing: due 15 days after first close; required to maintain Reg D exemption
- Blue sky compliance: most states require Form D or notice filing; California, New York require specific filings

---

## PRACTICE GUIDANCE: COMMON RED FLAGS AND STRATEGIC TIPS

### Formation Red Flags
- Founders not incorporated early enough (IP created before incorporation belongs to individuals)
- Missing 83(b) elections (30-day window; cannot be missed)
- Unsigned PIIAs from early contributors
- C-Corp not incorporated in Delaware before taking VC money

### Contract Red Flags
- Unlimited liability (no cap): walk away or negotiate hard
- Indemnification without carve-out from caps for data breaches
- Perpetual license grants to customer data without anonymization requirement
- No data portability/deletion on termination

### Privacy Red Flags
- Operating in California with 100K+ users and no CCPA/CPRA compliance program
- Using health data without BAA or HIPAA analysis
- Deploying children's apps without COPPA consent mechanisms
- No DPA for EU vendors/customers; no SCCs for international transfers

### AI/Employment Red Flags
- Using third-party AI hiring tools without disparate impact analysis (Title VII/state law exposure)
- Non-competes enforceable only in limited jurisdictions; over-reliance is problematic
- Worker misclassification in California under AB5

### Cybersecurity Red Flags
- Public company with no cybersecurity incident response plan and 4-day reporting timeline
- No state breach notification procedures
- No SOC 2 when enterprise customers are requiring it

---

## DISCLAIMER

All content in this skill is for educational and informational purposes only. It does not constitute legal advice and does not create an attorney-client relationship. Technology law is highly fact-specific and changes rapidly. Always consult qualified legal counsel for specific transactions, compliance decisions, or legal matters.

**Content is current as of mid-2026. Key sources include**: NVCA model documents (October 2025 update), EU AI Act (Regulation 2024/1689), SEC cybersecurity rules (July 2023), FTC non-compete rule vacatur (September 2025), Texas TRAIGA (June 2025), Colorado SB 26-189 (May 2026), BIS AI Diffusion Rule rescission (May 2025), DOJ v. Google remedies (September 2025), FTC v. Meta verdict (November 2025), HSR 2025 thresholds.
