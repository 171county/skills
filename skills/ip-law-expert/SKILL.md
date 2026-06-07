---
name: ip-law-expert
description: Expert IP law guidance for tech founders, engineers, and product teams covering patents, trademarks, copyright, trade secrets, open source licensing, AI-specific IP, freedom-to-operate analysis, due diligence, and international IP strategy. Use this skill when users ask about protecting inventions, choosing open source licenses, navigating AI copyright issues, handling IP in M&A or fundraising, defending against patent trolls, structuring contributor agreements, or any intellectual property question in a technology context.
---

# IP Law Expert for Tech Companies

> DISCLAIMER: This skill provides general legal information and frameworks for educational purposes only. Nothing here constitutes legal advice, and no attorney-client relationship is created. IP law is jurisdiction-specific and fact-dependent. Always consult a qualified IP attorney before making filing, licensing, or enforcement decisions.

## How to Use This Skill

When a user asks an IP question, identify the relevant domain (patent, trademark, copyright, trade secret, open source, AI, international, M&A) and provide:
1. The applicable legal framework and key concepts
2. Practical strategic options with tradeoffs
3. Decision matrices or checklists where applicable
4. Red flags and common mistakes
5. Recommended next steps (including when to involve an attorney)

---

## PART 1: THE FOUR PILLARS OF IP — COMPARISON MATRIX

| Dimension | Patent | Trademark | Copyright | Trade Secret |
|---|---|---|---|---|
| What it protects | Inventions, processes, designs | Brand identifiers (names, logos, sounds) | Original creative works | Confidential business information |
| Duration | 20 years (utility), 15 years (design) | Indefinite (with renewal + use) | Life + 70 years (US) | Indefinite (while secret) |
| Registration required? | Yes (USPTO/foreign offices) | No (common law), Yes (federal ® benefits) | No (automatic), recommended | No |
| Disclosure required? | Yes — full public disclosure | No | Yes — deposit copy for registration | No — secrecy is the protection |
| Key cost (US) | $15K–$50K+ (utility, with attorney) | $250–$400/class (TEAS Plus) | $65 online registration | Legal/operational costs only |
| Best for software | Business method patents, novel algorithms | App names, logos, domain brands | Source code, UX design, documentation | Algorithms, training data, internal processes |
| Enforcement | Sue for infringement | Opposition, TTAB, district court | DMCA takedowns, litigation | State/federal trade secret misappropriation |

---

## PART 2: PATENT LAW FOR TECH COMPANIES

### 2.1 Patent Types

**Utility Patents** — Cover new and useful processes, machines, articles of manufacture, or compositions. The workhorse of tech patent strategy. 20-year term from filing date.

**Design Patents** — Cover ornamental appearance of a functional item. Cheaper and faster than utility patents ($3K–$8K). Useful for protecting UI/UX layouts, icon designs, product industrial design. 15-year term.

**Provisional Patents** — Not a real patent; a 12-month placeholder that establishes a priority date. Costs $1,500–$4,000 with an attorney. Buys time to raise funding, refine the invention, and test market fit before committing to the full $15K–$50K non-provisional utility patent. Critical rule: the USPTO grants no extensions — missing the 12-month conversion deadline is fatal to that priority date.

### 2.2 Software Patent Strategy

Software patents require framing inventions as solving a technical problem through a technical solution (post-*Alice Corp. v. CLS Bank*, 2014). Abstract ideas implemented on a generic computer are not patentable. To survive USPTO and court scrutiny:

- Frame claims around specific technical improvements (e.g., "a method for reducing latency in distributed caching by..."), not business outcomes
- Include hardware-software interaction details
- Draft claims at multiple levels of abstraction (broad independent claims + narrow dependent claims)
- Document the technical problem being solved in the specification

**Patent vs. Trade Secret Decision Framework:**

| Factor | Favor Patent | Favor Trade Secret |
|---|---|---|
| Reverse-engineerable? | Yes → patent | No → trade secret |
| Expected useful life | < 20 years → trade secret | > 20 years possible → patent |
| Disclosure tolerance | Can disclose fully | Must keep secret |
| Enforcement budget | Adequate ($500K+) | Limited |
| Competitive moat | Broad market exclusion needed | Process-based advantage |
| Core example | Novel compression algorithm visible in output | Internal ML training pipeline |

### 2.3 Filing Strategy for Startups

**Stage-Gated Approach:**

1. **Pre-seed / Seed:** File provisional applications on core innovations. Cost: $2K–$5K per invention. Establishes priority date before investor pitches, product launches, or conference demos. Public disclosure before filing can permanently bar international patent rights.

2. **Series A:** Convert top provisional to non-provisional utility patents. File PCT application if international markets matter. Begin building a 3–5 patent portfolio around the core product.

3. **Growth Stage:** Expand portfolio defensively and offensively. File continuations and continuations-in-part to cover product evolution. Consider design patents on UI.

**The 12-Month Window Rule:** US law (35 U.S.C. § 102) grants inventors a 1-year grace period after public disclosure to file domestically — but most foreign countries (Europe, China, Japan) require absolute novelty (no public disclosure before filing). File before any public disclosure for maximum international protection.

### 2.4 PCT Applications — International Patents

The Patent Cooperation Treaty (PCT), administered by WIPO, allows a single international application that covers 157+ member countries and delays national-phase costs for up to 30 months from priority date.

**PCT Timeline:**
- Month 0: File home-country application (establishes priority date)
- Month 12: File PCT application (must be within 12 months of priority date)
- Month 18: International search report issued
- Month 30: National/regional phase entry deadlines (country-by-country examination begins)

**PCT Strategic Use:** Use the 30-month window to secure funding, validate markets, and decide which countries (typically US, EU, China, Japan, South Korea, Canada) warrant the $3K–$10K per-country national phase cost.

---

## PART 3: TRADEMARK LAW

### 3.1 Registration Strategy

**Common Law vs. Federal Registration:**
Using a mark in commerce creates common law ™ rights in the geographic area of use. Federal USPTO registration (®) provides:
- Nationwide constructive notice
- Legal presumption of ownership and exclusive right to use
- Ability to use ® symbol
- Basis for international filings via Madrid Protocol
- Customs recordation to block infringing imports
- Access to federal courts and enhanced damages

**TEAS Plus vs. TEAS Standard:** TEAS Plus ($250/class) requires using pre-approved ID of goods/services descriptions. TEAS Standard ($350/class) allows custom descriptions. Use TEAS Plus when possible.

**Trademark Classes:** USPTO uses 45 Nice Classification classes. Tech companies typically file in Class 9 (software), Class 35 (SaaS/business services), Class 42 (technology services, cloud services). File in all classes where you operate or plan to operate.

### 3.2 Likelihood of Confusion — the DuPont Factors

The USPTO and courts evaluate trademark conflicts using the DuPont factors (13 total, with these being most critical for tech):
1. **Similarity of the marks** — appearance, sound, meaning
2. **Relatedness of goods/services** — do they compete or co-exist?
3. **Channels of trade** — same distribution, same customers?
4. **Conditions of purchase** — sophisticated buyers vs. impulse buyers
5. **Strength of the senior mark** — arbitrary/fanciful > suggestive > descriptive

**Trademark Strength Spectrum (strongest → weakest):**
- Fanciful (invented words): KODAK, GOOGLE, ZOOM
- Arbitrary (real word, unrelated meaning): APPLE (computers), AMAZON (retail)
- Suggestive (implies quality): NETFLIX, MICROSOFT
- Descriptive (describes the product): harder to register, requires secondary meaning
- Generic: never registrable (e.g., "Computer Software" for software)

### 3.3 Trade Dress and Domain Names

**Trade Dress** protects the visual appearance of products/services (UI design, app icon style, color schemes if distinctive). Must show non-functionality and acquired distinctiveness.

**Domain Name Disputes:** Use ICANN's Uniform Domain-Name Dispute-Resolution Policy (UDRP) to recover domain names registered in bad faith. Must prove: (1) domain is identical/confusingly similar to your mark, (2) registrant has no legitimate interests, (3) registered and used in bad faith. Faster and cheaper than litigation ($1,500–$5,000 vs. $100K+ lawsuit).

### 3.4 Madrid Protocol — International Trademark Filing

File one international application through the USPTO (or EUIPO) to designate 132+ countries. Requirements: must have a base application or registration in your home country.

**Cost Structure:** Basic WIPO fee (653 CHF standard, 903 CHF color mark) + per-country designation fees. Total for 5 major markets: approximately $3,000–$6,000. Compare to direct national filings ($1,000–$3,000 per country with local counsel).

**Central Attack Risk:** If the basic (home country) registration is cancelled within 5 years of international registration, all Madrid designations fall. File a separate national application in key markets to hedge.

---

## PART 4: COPYRIGHT LAW FOR SOFTWARE

### 4.1 Software Copyright Basics

Copyright attaches automatically to original source code, object code, documentation, UI design elements, and training datasets (to the extent they reflect creative selection/arrangement). No registration required for protection, but US registration is required to sue for infringement and to recover statutory damages ($750–$150,000 per work) and attorney fees.

**What copyright protects:** Literal copying of code, non-literal elements (structure, sequence, organization) under *Oracle v. Google* (2021 — APIs can be protected but extensive use may qualify as fair use).

**What copyright does not protect:** Ideas, algorithms, mathematical formulas, functionality (those require patents), purely functional elements.

### 4.2 Open Source License Compatibility Chart

| License | Copyleft Strength | Can Link With Proprietary? | Patent Grant? | Can Relicense? | Best Use Case |
|---|---|---|---|---|---|
| MIT | None (permissive) | Yes | No explicit | Yes | Maximum adoption, libraries |
| Apache 2.0 | None (permissive) | Yes | Yes (explicit) | Yes | Corporate-friendly, patent protection |
| GPL v2 | Strong | No | Implicit | No (w/o CLA) | Community software, copyleft enforcement |
| GPL v3 | Strong | No | Yes (explicit) | No (w/o CLA) | Anti-tivoization, strong copyleft |
| LGPL v3 | Weak/Library | Yes (dynamic linking) | Yes | Limited | Libraries used in proprietary apps |
| AGPL v3 | Network copyleft | No (network use triggers) | Yes | No (w/o CLA) | SaaS/cloud services, anti-cloud loophole |
| MPL 2.0 | File-level copyleft | Yes (separate files) | Yes | Limited | Mozilla-style file-level copyleft |
| BSL 1.1 | Non-OSI | Production restrictions | Varies | Auto-converts (4yr) | Source-available, anti-cloud |
| SSPL v1 | Service copyleft | No (service use triggers) | Yes | No | MongoDB-style anti-cloud protection |

**License Compatibility Rules:**
- MIT code can go into Apache 2.0, GPL, and proprietary projects
- Apache 2.0 code can go into GPL v3 but NOT GPL v2 (patent clause conflict)
- GPL v2 code cannot be mixed with GPL v3 without "or later" clause
- AGPL code in a SaaS app requires the entire service's source to be released
- Proprietary code can never incorporate GPL/AGPL code without a commercial license

### 4.3 Fair Use in AI Training (2024–2025 Developments)

US courts in 2024–2025 have issued landmark decisions on AI training and fair use:

**Thomson Reuters v. ROSS Intelligence (Feb 2025):** Delaware federal court held ROSS's use of Westlaw headnotes to train a legal search AI was **copyright infringement**, rejecting fair use. Key factor: direct commercial competition with the original.

**Bartz v. Anthropic (June 2025):** San Francisco federal court held Anthropic's use of books to train Claude was **fair use**. Senior Judge Alsup found the use highly transformative (training to produce new creative output, not reproduce books).

**Kadrey v. Meta (June 2025):** Court found Meta's use of pirated "shadow library" books to train Llama was **fair use** as "highly transformative."

**NYT v. OpenAI (ongoing):** Motion to dismiss denied March 2025. Case in discovery phase. OpenAI ordered to preserve output logs. No fair use ruling expected before summer 2026. High stakes: if NYT prevails, it could establish that verbatim regurgitation of copyrighted content in AI outputs is actionable even if training is fair use.

**US Copyright Office Part 3 Report (May 2025):** Concluded AI training on copyrighted materials may constitute prima facie infringement. Transformative use arguments are not inherently valid. Emphasized licensing as the preferred path forward.

**Practical Guidance for AI Companies:**
- Document the transformative purpose of training
- Implement filtering to prevent verbatim reproduction of copyrighted content in outputs
- Negotiate licenses with major content owners where possible
- Maintain records of training data sources for potential disclosure requirements
- Consider opt-out mechanisms for content creators

---

## PART 5: TRADE SECRETS

### 5.1 Legal Framework

The **Defend Trade Secrets Act (DTSA, 2016)** created federal civil claims for trade secret misappropriation, supplementing state laws based on the Uniform Trade Secrets Act (UTSA). For something to qualify as a trade secret it must:
1. Derive independent economic value from not being generally known
2. Be subject to reasonable measures to maintain secrecy

### 5.2 Protection Stack

| Layer | Mechanism | Implementation |
|---|---|---|
| Legal | NDAs with DTSA whistleblower notice | All employees, contractors, vendor agreements |
| Legal | IP assignment agreements | Employment offers, contractor agreements |
| Legal | Non-solicitation clauses | Check enforceability by state (California restricts non-competes) |
| Technical | Access controls / least privilege | Role-based access, audit logs |
| Technical | Encryption at rest and in transit | For source code, data, models |
| Technical | Code repositories with access logs | Private repos, branch protection |
| Operational | "Confidential" marking on documents | Reserve for actual secrets |
| Operational | Employee offboarding protocol | Return of materials, exit interviews, account revocation |
| Operational | Vendor security assessments | Third-party access reviews |

**Critical DTSA Requirement:** Every NDA and employment agreement governing trade secrets must include the DTSA whistleblower immunity notice: employees who disclose trade secrets to attorneys or government officials in reporting illegal activity are immune from DTSA liability. Failure to include this notice eliminates your right to recover exemplary damages (2x) and attorney fees.

### 5.3 Patent vs. Trade Secret — Final Decision Framework

Use trade secrets when: the innovation is embedded in a process (not a product), competitors cannot reverse-engineer it, the competitive advantage may last longer than 20 years, or you lack the budget for patent litigation. Classic examples: Google's search algorithm, Coca-Cola's formula, ML model training pipelines, proprietary datasets.

Use patents when: the innovation is visible in the product and reverse-engineerable, you need to license or enforce publicly, investors expect a patent portfolio, or you plan an M&A exit where patents are valued.

---

## PART 6: IP FOR ARTIFICIAL INTELLIGENCE

### 6.1 Ownership of AI-Generated Content

**Current US Position (2024–2025):** The US Copyright Office maintains that copyright requires human authorship. Works created solely by AI without human creative control are not copyrightable. The Supreme Court declined to hear a challenge to this principle in March 2026.

**What can be protected:**
- Human-authored prompts (potentially, if creative enough)
- Human selection, arrangement, and curation of AI outputs
- AI-assisted works where the human exercised meaningful creative control over final expression
- The AI model weights themselves (as a software work) — owned by the developer
- Training datasets (copyright in selection/arrangement, not raw facts)

**Practical implication:** AI companies own their models (copyright + trade secret); they do not own the outputs unless humans contributed creatively to those specific outputs.

### 6.2 AI Patent Strategy

AI inventions are patentable when framed as technical solutions to technical problems. The USPTO's 2024 guidance on AI-assisted inventions confirms: AI cannot be a named inventor (Thaler v. Vidal, Fed. Cir. 2022). Human inventors must be listed. AI tools can assist in invention, but the legal inventive contribution must come from humans.

**Patentable AI areas:** novel neural network architectures, specific training methods, inference optimization techniques, hardware-software integration for AI, specific applications of AI to solve technical problems.

**Not patentable (post-*Alice*):** "Apply AI to [business process]" abstract without technical specificity.

### 6.3 Training Data Rights

**Copyright issues:** Using copyrighted content to train AI models is currently the most litigated area of IP law. The emerging consensus from 2025 cases is that training alone may qualify as fair use for transformative generative systems, but verbatim reproduction in outputs creates separate infringement risk.

**GDPR and AI Training (EU):** The European Data Protection Board's Opinion 28/2024 confirmed GDPR applies to AI models trained on personal data due to memorization capabilities. Companies must:
- Identify a valid legal basis (legitimate interest requires detailed balancing test)
- Conduct Data Protection Impact Assessments (DPIAs) for high-risk processing
- Implement data minimization and anonymization
- Respond to data subject rights requests (right to erasure creates tension with model training)

The EU Commission is proposing to codify legitimate interest as a valid basis for AI training, but this remains in process as of mid-2026.

**Italy's Garante fined OpenAI €15 million** (2024) for failing to establish lawful basis for processing personal data used in ChatGPT training — a warning to all AI companies operating in Europe.

### 6.4 Model Weights and Trade Secrets

Model weights are the most valuable IP asset for AI companies. They should be protected as trade secrets through:
- Access controls and encrypted storage
- Watermarking techniques to detect unauthorized copying
- API-only access (avoid releasing weights publicly unless strategically intentional)
- Contractual restrictions on reverse engineering in API terms

---

## PART 7: OPEN SOURCE STRATEGY

### 7.1 License Selection Framework

**Decision Tree:**
1. Is adoption/distribution the primary goal? → MIT or Apache 2.0
2. Do you need explicit patent protection? → Apache 2.0 (over MIT)
3. Do you want contributions to stay open? → GPL v3 or AGPL v3
4. Is your product a library used by proprietary apps? → LGPL v3 or MPL 2.0
5. Is it a SaaS/cloud product and you want to prevent free cloud hosting? → AGPL v3 or BSL 1.1
6. Do you want dual licensing (open source + commercial)? → Use GPL or AGPL with CLA

### 7.2 Dual Licensing Strategy

The dual licensing model (pioneered by MySQL/Qt, adopted by MongoDB, Redis, HashiCorp) allows:
- **Community edition** under GPL/AGPL: attracts developers, builds ecosystem
- **Commercial license**: for enterprises that cannot comply with copyleft obligations

**Requirements:** You must own or have re-licensing rights to all code in the project. This requires either:
- A **Contributor License Agreement (CLA)** that grants you relicensing rights
- Complete company ownership (no third-party contributions under copyleft terms)

**2024 Trend — Source-Available Licenses:** Companies like Redis (RSALv2/SSPL), HashiCorp (BSL), and Elastic (SSPL) moved away from true open source to source-available licenses to prevent cloud providers (AWS, Azure, GCP) from offering hosted versions without contributing back.

### 7.3 Contributor Agreements — CLA vs. DCO

| Aspect | CLA (Contributor License Agreement) | DCO (Developer Certificate of Origin) |
|---|---|---|
| Legal mechanism | Signed contract | Per-commit sign-off attestation |
| Enables relicensing? | Yes | No |
| Friction for contributors | High (signing workflow required) | Low (git commit -s) |
| Corporate contributor coverage | CCLA covers employer rights | No employer sign-off mechanism |
| Dispute strength | Strong (signed contract) | Weaker |
| Best for | Dual-licensed projects, foundation projects | Community-driven projects, Linux-kernel style |
| Examples | Apache, Google, Microsoft OSS | Linux kernel, GitLab, many smaller projects |

**Recommendation:** Use CLA if you anticipate dual licensing or need to relicense. Use DCO if contributor friction reduction is more important than relicensing rights (e.g., purely GPL projects that will never go commercial).

---

## PART 8: FREEDOM-TO-OPERATE (FTO) ANALYSIS

FTO analysis determines whether you can build, sell, or use a technology in a given market without infringing active third-party patents.

### 8.1 FTO Process

**Stage 1 — Concept (20–40 hours):**
- Decompose the product into functional features
- Identify technology landscape and major patent holders
- Run high-level keyword and classification searches (USPTO, EPO, Google Patents)
- Identify obvious blocking patents

**Stage 2 — Development (100–200 hours with patent attorney):**
- Detailed claim-by-claim analysis of identified patents
- Determine if each claim reads on your product elements
- Assess patent validity (prior art challenges)
- Map risk levels: high / medium / low

**Stage 3 — Risk Mitigation Options:**
1. **Design around:** Modify the product to avoid the claim
2. **License:** Negotiate a license with the patent holder
3. **Invalidate:** Challenge the patent via Inter Partes Review (IPR) at USPTO ($40K–$100K)
4. **Opinion of counsel:** Obtain written FTO opinion to establish good faith and negate willful infringement (3x damages exposure)
5. **Acquire:** Buy the blocking patent

### 8.2 When to Conduct FTO

- Before product launch in a new technology area
- Before significant R&D investment in a new feature
- During M&A due diligence (both sides)
- When entering a new geographic market
- After receiving a cease-and-desist letter

---

## PART 9: IP IN EMPLOYMENT AND CONTRACTOR AGREEMENTS

### 9.1 Essential IP Clauses

**For Employees:**

1. **Invention Assignment:** All work-related inventions, discoveries, and IP created during employment (and often for a period after) are automatically assigned to the company. Must be signed before employment begins.

2. **Prior Inventions Carve-Out:** Employees list any pre-existing inventions they own before joining. This prevents the company from claiming pre-employment IP and protects the employee.

3. **Moonlighting Restriction:** Prohibits working on competitive projects during off-hours using company resources.

4. **DTSA Whistleblower Notice** (required in any agreement covering trade secrets).

**California Exception:** California Labor Code § 2870 limits employer IP claims to inventions developed using employer resources or related to current/anticipated company business. Employees in California retain rights to purely personal inventions.

**For Contractors / Consultants:**
- "Work for hire" doctrine applies narrowly to specific categories under copyright law
- Custom software developed by an independent contractor is NOT automatically work for hire
- Always include explicit IP assignment clause: "Contractor hereby assigns to Company all right, title, and interest in..."
- Include representations that the work does not infringe third-party IP

### 9.2 Founder IP Assignment

Before any funding round, all founders must assign pre-incorporation IP to the company. Common failure mode: IP was created before the company was incorporated and sits with individual founders.

Standard equity structure with vesting serves a dual purpose: aligns incentives AND ensures IP ownership follows equity — if a founder leaves before the cliff, the company retains the IP tied to their unvested equity.

**4-year vesting / 1-year cliff:** 25% vests at month 12, then monthly over 36 months. Post-cliff, ensure vesting agreements include IP carve-back provisions on departure.

---

## PART 10: PATENT TROLLS AND DEFENSIVE STRATEGIES

### 10.1 Threat Landscape

Non-Practicing Entities (NPEs), pejoratively called "patent trolls," hold patents solely to extract licensing fees or settlements. They target tech companies of all sizes, with average costs of $1.5M–$4M to defend a patent lawsuit to verdict.

### 10.2 Defensive Arsenal

**1. Own Your Own Patents**
A patent portfolio is the primary deterrent. NPEs rarely sue companies with large patent portfolios because of cross-licensing and retaliation risk.

**2. Defensive Patent Aggregators**
- **Open Invention Network (OIN):** Free membership; royalty-free cross-license on Linux-related patents. 3,000+ members.
- **LOT Network:** Members agree that if they sell a patent, the buyer cannot assert it against other members. $2,000–$20,000/year.
- **Allied Security Trust (AST):** Fee-based acquisition of threatening patents before NPEs can buy them.

**3. Defensive Publication (Prior Art)**
Publish detailed technical disclosures of innovations you do not want to patent — this creates prior art that prevents competitors AND NPEs from patenting the same concepts. Tools:
- IP.com Prior Art Database
- ArXiv preprints
- Technical blog posts with clear timestamps

**4. Inter Partes Review (IPR)**
Challenge the validity of a troll's patent at the USPTO Patent Trial and Appeal Board (PTAB). Success rate: ~60–70% of challenged claims are invalidated. Cost: $40K–$100K. Must be filed within 1 year of service of infringement complaint.

**5. America Invents Act (AIA) Covered Business Method (CBM) Review**
Available for patents related to financial products or services.

**6. License When Economical**
For nuisance suits, a $50K–$200K license is often cheaper than litigation. Track and document all licensing agreements for future M&A.

---

## PART 11: IP DUE DILIGENCE — INVESTOR AND ACQUIRER CHECKLIST

Investors and acquirers conduct IP due diligence to verify ownership, assess validity, and identify risks that could destroy enterprise value.

### 11.1 What Investors Look For (Series A+)

- [ ] All IP is owned by the company entity, not individual founders or employees
- [ ] Signed IP assignment agreements from all founders, employees, and contractors
- [ ] Patent applications filed and prosecution history clean
- [ ] No unresolved IP disputes or pending litigation
- [ ] Open source compliance audit — no AGPL or GPL code in proprietary products without license
- [ ] Trademarks registered in key markets
- [ ] Domain names owned by company
- [ ] All third-party software licenses reviewed and compliant

### 11.2 M&A IP Due Diligence Checklist

**Ownership Chain:**
- [ ] Complete list of all patents, trademarks, copyrights, domain names, and trade secrets
- [ ] IP assignments from all founders, employees, and contractors (verify signatures and dates)
- [ ] No "work made for hire" gaps — contractor work without explicit assignment
- [ ] Prior inventions lists from all technical employees

**Patent Portfolio:**
- [ ] Status of all patent applications and issued patents
- [ ] Maintenance fees current
- [ ] No prior art issues identified in prosecution history ("prosecution history estoppel" review)
- [ ] Freedom-to-operate opinion obtained for core product

**Open Source:**
- [ ] OSS audit conducted (tools: FOSSA, Black Duck, Snyk)
- [ ] No GPL/AGPL code embedded in proprietary products
- [ ] All OSS licenses complied with (attribution, notices, source availability)
- [ ] Contributor agreements in place (CLA or DCO)

**Third-Party IP:**
- [ ] All software license agreements reviewed
- [ ] Data license agreements for training data and datasets
- [ ] No infringement claims received or threatened
- [ ] API terms of service compliance (no scraping, no ToS violations in training data)

**Trade Secrets:**
- [ ] NDA program in place for employees, contractors, vendors
- [ ] Access controls documented
- [ ] Employee offboarding procedures

**Red Flags That Kill Deals:**
- IP owned by founders personally, not the company
- GPL code in proprietary codebase without commercial license
- Missing IP assignments from key inventors
- Unresolved infringement claims
- Training data of questionable provenance for AI companies

---

## PART 12: INTERNATIONAL IP STRATEGY

### 12.1 Patent — PCT vs. Direct Filing

| Route | PCT | Direct National |
|---|---|---|
| Coverage | 157+ countries via one application | Country-by-country |
| Cost phase | Deferred until month 30 | Immediate in each country |
| Flexibility | Maximum — decide markets at month 30 | Less flexible |
| Best for | Uncertain market strategy | Known key markets (US, EU, CN, JP) |
| Priority period | 12 months from first filing | 12 months from first filing |

**Priority Markets for Tech:** US (largest market), EU (via European Patent Office / national validation), China, Japan, South Korea, Canada. Budget $5K–$15K per country for national phase + local counsel.

### 12.2 Trademark — Madrid Protocol

File through USPTO after receiving US registration/application. Designate member countries. WIPO forwards to each national office for examination under local law.

**Key markets for designation:** EU (via EUIPO, one designation covers 27 countries), UK (post-Brexit, separate designation), China (CNIPA), Japan (JPO), Canada (CIPO), Australia (IP Australia).

**Hague Agreement (Design Patents / Industrial Designs):** Similar to Madrid Protocol for industrial designs. One WIPO application for design protection in 96+ countries. Relevant for UI design, product design, hardware design.

---

## PART 13: LICENSING STRATEGIES FOR TECH PRODUCTS

### 13.1 SaaS Licensing

In a SaaS model, customers access software as a service — the IP stays with the provider. Key provisions in SaaS agreements:

- **Grant:** Limited, non-transferable, non-sublicensable license to access the service
- **Data ownership:** Customer retains ownership of their data; provider owns the platform, models, and derived aggregate insights
- **Restrictions:** No reverse engineering, no competitive benchmarking without consent
- **API rate limits:** Metering, throttling, and acceptable use policies
- **Output rights:** Define who owns AI-generated outputs (typically customer owns outputs from their inputs, provider owns the model)

### 13.2 API Licensing

Key clauses for API license agreements:
1. **Scope of use:** Authorized use cases, prohibited uses (competitive analysis, scraping, reselling)
2. **Rate limits and SLAs:** Metering, throttling, uptime commitments
3. **Data handling:** What happens to request/response data; training on customer data opt-out
4. **Derivative works:** Who owns integrations and applications built on the API
5. **Termination and API sunset:** Notice periods, data portability

### 13.3 SDK Licensing

SDKs bundled with applications raise open source contamination risk. Due diligence:
- Audit all third-party OSS in the SDK
- Ensure Apache 2.0 / MIT for maximum compatibility with proprietary apps
- AGPL in an SDK is a serious risk — it can contaminate the app distributing it
- Include clear license file and attribution notices

---

## PART 14: COMMON MISTAKES AND RED FLAGS

| Mistake | Risk | Fix |
|---|---|---|
| No IP assignment from founders at incorporation | Company doesn't own its core IP | Execute IP assignment agreements immediately; retroactive is possible but messier |
| Using GPL code in commercial SaaS | Copyleft may require open-sourcing the whole product | Conduct OSS audit; replace or obtain commercial license |
| Disclosing invention before filing | Bars foreign patent rights; starts 1-year US clock | File provisional first, always |
| Not including DTSA whistleblower notice in NDAs | Forfeits exemplary damages and attorney fees | Update all templates immediately |
| No CLA on open source project | Cannot dual-license or relicense later | Implement CLA retroactively (contact all contributors) |
| Contractor work without IP assignment | Copyright stays with contractor | Always include explicit assignment clause; "work for hire" alone is insufficient |
| Trademark in one class only | Infringers operate in unregistered class | File in all classes of actual and planned use |
| Relying on NDAs alone as trade secret protection | NDAs without operational security may be insufficient "reasonable measures" | Layer NDAs with access controls, encryption, and documented security practices |
| Ignoring open source in AI training data | Copyright and license compliance exposure | Conduct training data provenance audit |
| No FTO before product launch | Infringement exposure from day one | Conduct FTO analysis pre-launch for novel technology areas |

---

## PART 15: QUICK REFERENCE — STARTUP IP PRIORITY SEQUENCE

**Day 0 (Formation):**
- IP assignment agreements signed by all founders
- Prior inventions lists completed
- Founder equity with 4-year vesting / 1-year cliff

**Pre-Launch:**
- File provisional patent(s) on core innovations
- Conduct trademark clearance search
- File trademark application(s)
- Run OSS audit on codebase
- Review all third-party licenses
- Draft employee IP assignment and NDA templates (include DTSA notice)
- Register copyrights on core software (if valuable enough to litigate)

**Post-Launch / Series A:**
- Convert provisional to non-provisional utility patent
- File PCT application if international markets relevant
- Build patent portfolio (3–5 patents minimum for Series B/C)
- Implement formal trade secret protection program
- Consider joining LOT Network or OIN
- Conduct FTO analysis on core product

**M&A / IPO Prep:**
- Full IP audit and clean-up
- Close any ownership gaps (missing assignments, etc.)
- OSS compliance certification (FOSSA or equivalent)
- Obtain FTO opinion of counsel
- Trademark registrations current in all key markets
- IP represented accurately in data room

---

*This skill provides general legal information only. For specific legal advice on IP strategy, filing decisions, enforcement, or licensing, consult a licensed intellectual property attorney. IP law varies by jurisdiction and the law is evolving rapidly, particularly in AI-related areas.*
