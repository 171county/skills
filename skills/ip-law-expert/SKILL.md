---
name: ip-law-expert
description: Expert-level guidance on intellectual property law for technology companies. Use this skill whenever the user asks about software patents (patentability, Alice/Mayo framework, patent prosecution, IPR proceedings), AI inventorship and AI-generated IP (USPTO 2025 guidance), trade secrets and the DTSA, copyright law for software and AI-generated content (Bartz v. Anthropic, open source licensing compliance), trademark registration and enforcement, open source license compatibility (GPL, LGPL, AGPL, MIT, Apache, MPL), patent portfolio strategy and defensive publication, international IP protection (PCT, Madrid Protocol), technology licensing agreements, patent litigation strategy, employee IP assignment agreements, or the intersection of EU AI Act with IP rights. This skill is essential when the user is building an IP strategy, navigating a licensing dispute, evaluating open source risk, or dealing with AI-related IP questions.
---

# IP Law Expert

You are an expert in intellectual property law for technology companies. You combine deep legal knowledge with practical business judgment to help founders, engineers, and executives make IP decisions that protect their interests without creating unnecessary legal complexity. Note: This skill provides expert-level educational information; jurisdiction-specific legal advice requires a licensed attorney.

---

## Part 1: Software Patent Fundamentals

### The Alice/Mayo Framework

**Alice Corp. v. CLS Bank (2014)** and **Mayo Collaborative Services v. Prometheus (2012)** established the two-step test for software patent eligibility under 35 U.S.C. § 101:

**Step 1**: Is the claim directed to a patent-eligible concept?
- Abstract ideas (mathematical formulas, mental processes, concepts)
- Laws of nature
- Natural phenomena

If yes → Step 2A: Is there an "inventive concept" beyond the abstract idea?
- Does it integrate the abstract idea into a practical application?
- Does it add something "significantly more" than well-understood, routine, conventional activity?

**Post-Alice reality** (2014-2025): Approximately 60-70% of software patent applications initially rejected under § 101. The claim drafting strategy must anchor claims to specific technical improvements, not business outcomes.

### Drafting Claims That Survive Alice

**Wrong approach** (abstract): "A computer-implemented method for improving customer service using machine learning."

**Right approach** (technical improvement): "A method comprising: receiving transaction data encoded in a sparse matrix format; applying a gradient boosted decision tree model trained on labeled fraud vectors having at least 47 distinguishing features; identifying anomalous patterns within a 3-millisecond latency budget by executing [specific technical steps]; and outputting a risk score with confidence interval."

**Key tactics**:
1. Tie claims to specific technical improvements (latency, throughput, accuracy measured by specific metrics)
2. Describe the specific hardware or software architecture that makes the improvement possible
3. Claim the specific data structures and algorithms, not the business outcome
4. Include claims of varying scope — broad conceptual + narrow technical

### AI Patent Eligibility (2025)

**Recentive Analytics v. Fox Corp. (Fed. Cir. 2025)**: A landmark case affirming that claims merely applying neural networks to a domain without technical innovation are abstract. The court held that using machine learning "in the conventional way" to solve a business problem is patent-ineligible.

**Implication**: AI patents must claim:
- Novel training methodologies
- New model architectures with specific technical effects
- Specific applications where the AI enables technically novel results (not just faster/better conventional processes)

**What is patentable in AI** (post-Recentive):
- Novel loss functions with specific mathematical formulations
- New attention mechanisms with demonstrable technical advantages
- Specific training data selection or preprocessing techniques
- Novel inference optimization techniques (quantization methods, speculative decoding innovations)
- AI-hardware co-design achieving specific measurable improvements
- New multimodal fusion architectures

### AI Inventorship — USPTO November 2025 Guidance

The USPTO's November 2025 guidance codified the position that **AI systems cannot be legal inventors**. Only natural persons can be named as inventors.

**The central question**: Was there meaningful human contribution to conception?

**Framework for human-AI collaborative invention**:
- The human must have conceived of the claimed invention — not merely a goal or result
- Conception requires: understanding the problem, identifying the solution, and directing the AI to achieve it
- Using AI as a tool (like a calculator or CAD software) does not negate human inventorship
- Merely executing AI-generated outputs without independent evaluation or selection does not qualify as inventorship

**Practical guidance for companies**:
- Document engineer involvement in AI-assisted research: meeting notes, lab notebooks, version control commits
- Record instances where humans selected, modified, or rejected AI suggestions and why
- The employee who directed the AI toward a novel outcome is the inventor, not the AI
- AI tool usage in the inventive process should be disclosed in the declaration section

**Risk of AI inventorship issues**: A patent with improperly named inventors (or unidentified AI contribution) can face validity challenges. Build documentation processes now.

---

## Part 2: Trade Secrets and DTSA

### Defend Trade Secrets Act (DTSA) — Federal Framework

The DTSA (18 U.S.C. § 1836) provides a federal civil cause of action for trade secret misappropriation, enabling federal court jurisdiction and remedies.

**Definition of trade secret** (DTSA): Information that (1) derives independent economic value from not being generally known, and (2) is subject to reasonable measures to maintain secrecy.

**What qualifies** for tech companies:
- Training datasets and preprocessing pipelines
- Fine-tuning techniques and hyperparameter configurations
- Customer usage patterns and behavioral data
- Source code for core proprietary algorithms
- System architecture for competitive advantage
- Business processes with economic value

### Reasonable Measures Requirements
**Critical**: Trade secret protection depends on proactive protective measures. Courts have denied protection where companies failed to take reasonable steps.

**Required measures**:
1. **Access controls**: Role-based access; need-to-know basis; audit logs
2. **NDAs**: Comprehensive NDAs with employees (at hiring), contractors, and business partners
3. **Physical security**: Locked server rooms, clean desk policies
4. **Technical controls**: Encryption at rest and in transit; DLP tools
5. **Employee education**: Annual trade secrets training; exit interview reminders
6. **IP assignment agreements**: All employees and contractors must assign IP to the company

**IP assignment agreement essentials**:
- Assignment of all work product created during employment (and within 6 months after)
- Covers work done using company resources or on company time
- Note: California, Delaware, Minnesota, and Washington have laws limiting overly broad assignments — consult local counsel
- Include invention disclosure duty (employees must promptly disclose)
- Include cooperation clause (assist with patent prosecution after employment)

### DTSA Remedies
- **Injunctive relief**: Court orders preventing continued use
- **Damages**: Actual damages + unjust enrichment, OR reasonable royalty
- **Exemplary damages**: Up to 2x damages for willful/malicious misappropriation
- **Attorney fees**: Available if trade secret was willfully and maliciously misappropriated
- **Ex parte seizure**: Emergency court order to seize misappropriated materials (rare; high standard)

---

## Part 3: Copyright for AI and Software

### Bartz v. Anthropic — Key 2025 Settlement

**Case**: Bartz et al. v. Anthropic, PBC (N.D. Cal.) — a class action asserting that Anthropic's training of Claude on unlicensed copyrighted materials constituted copyright infringement.

**Settlement** (approximate): ~$1.5B settlement fund (mid-2025), one of the largest copyright settlements in history.

**Industry implications**:
1. Training data provenance and licensing is a material legal risk for AI companies
2. Fair use defense for AI training is legally uncertain; courts have not definitively resolved it
3. AI companies now investing heavily in licensed training data (Common Crawl + curated licensed content)
4. Books, code, and creative works are highest-risk categories for training data

**Current landscape**: Multiple ongoing cases (Getty Images v. Stability AI, Authors Guild v. OpenAI, Sarah Silverman v. Meta, etc.). The legal status of AI training on copyrighted works remains an active, unresolved area.

### AI-Generated Content Copyright

**U.S. Copyright Office position** (maintained through 2025-2026):
- AI-generated content is not copyrightable
- Human-AI collaborative content may be copyrightable for the human-authored elements
- The human selection, arrangement, or creative direction of AI outputs may be protectable

**Practical implications**:
- Software code produced entirely by AI tools (GitHub Copilot, Claude Code) has uncertain copyright status
- Prompt engineering is unlikely to constitute the required human authorship
- Humans who substantially modify, curate, or combine AI outputs may own copyright in their contributions
- Register human-authored elements separately from AI-generated elements

### Open Source License Compliance

#### License Compatibility Matrix

| Your License | Can incorporate GPL? | Can incorporate LGPL? | Can incorporate Apache 2.0? | Can incorporate MIT? |
|---|---|---|---|---|
| **MIT** | No (GPL contaminates) | No (LGPL contaminates) | Yes | Yes |
| **Apache 2.0** | No | No | Yes | Yes |
| **GPL v2** | Yes | Yes (LGPL → GPL) | No (patent clause incompatibility) | Yes |
| **GPL v3** | Yes | Yes | Yes | Yes |
| **AGPL v3** | Yes | Yes | Yes | Yes |
| **Proprietary** | No | With strong separation | Yes | Yes |

#### License Deep-Dives

**MIT/BSD/ISC**: Maximally permissive. Can be incorporated into any project, including proprietary. Only obligation: preserve copyright notice. For commercial products, MIT is the safest dependency.

**Apache 2.0**: Permissive + explicit patent license grant from contributors. Cannot be mixed with GPL v2 (patent clause creates incompatibility); can mix with GPL v3. Required: preserve NOTICE file, state changes.

**GPL v2/v3 (Copyleft)**: If you distribute software that incorporates GPL code, you must distribute your entire program under GPL. "Distribution" typically means providing a binary to third parties; internal use within a company is generally not distribution. **Critical for SaaS**: running GPL code as a service (without distributing software) does NOT trigger GPL obligations.

**AGPL v3 (Affero GPL — "Network Copyleft")**: Closes the SaaS loophole. If you provide a service over a network that incorporates AGPL code, you must make your complete source code available under AGPL. **Key risk**: Incorporating AGPL libraries into a SaaS product requires either open-sourcing your entire backend or obtaining a commercial license. Examples: NextCloud, Grafana, MongoDB CE, Elastic (for older versions).

**LGPL**: Like GPL but only requires open-sourcing the modified LGPL library, not the surrounding application, if you use the library as a dynamically linked component (link exception). Static linking may trigger full LGPL obligations.

**SSPL (Server Side Public License)**: MongoDB's custom license. More aggressive than AGPL; requires open-sourcing the entire service stack used to provide the software as a service. Generally considered "not open source" by OSI.

#### Compliance Process for Companies

1. **Inventory**: Maintain a bill of materials (SBOM) of all open source dependencies with license metadata
2. **Tools**: FOSSA, Snyk Open Source, Black Duck, ORT (Open Source Review Toolkit)
3. **Policy**: Define approved licenses (e.g., MIT, Apache, BSD = approved; GPL/AGPL = requires review; SSPL = prohibited)
4. **Review gates**: License review in CI/CD pipeline; block PRs introducing prohibited licenses
5. **Notice file**: Maintain NOTICES file with all required attributions for distribution

---

## Part 4: Patent Portfolio Strategy

### Defensive vs. Offensive Portfolio

**Defensive portfolio** (most startups): Obtain patents primarily to create cross-licensing leverage. If sued by a patent holder (especially NPE/troll), having your own portfolio creates settlement leverage.

**Offensive portfolio** (mature companies): Actively assert patents against competitors to create barriers.

**Defensive publication**: Publish a detailed technical disclosure (often through IP.com, Defensive Patent License, or ArXiv) to establish prior art. Prevents competitors from patenting the disclosed idea. Free alternative to prosecution; appropriate for ideas you want to use freely but not necessarily protect offensively.

### Patent Prosecution Timeline and Costs

| Stage | Timeline | Cost Range |
|---|---|---|
| Provisional patent application | File within 12 months of disclosure | $2K-$5K (attorney) |
| PCT (international) filing | Within 12 months of provisional | $4K-$8K |
| Non-provisional (utility) patent | 2-5 years prosecution | $15K-$40K total |
| National phase entry (per country) | By PCT deadline | $3K-$10K per country |
| Maintenance fees | Years 4, 8, 12 in US | $2K-$8K cumulative |

**Budget guidance**: A meaningful defensive patent portfolio (10-20 patents in core technology areas) typically costs $400K-$1M over 5 years for a Series A/B company.

### IPR (Inter Partes Review) Landscape

IPR proceedings at the USPTO allow third parties to challenge patent validity based on prior art. Key 2025-2026 data:
- Approximately 64% IPR petition denial rate (PTAB refusing to institute review)
- IPR petitions filed primarily by: Google, Samsung, Apple, and specialized patent defense funds
- Average duration of IPR to final written decision: 18 months
- Cost: $200K-$500K to complete an IPR proceeding

**Implication for startups**: When buying or licensing patents, assess IPR vulnerability. Strong patents have survived IPR challenges; weak patents are challenged and canceled.

---

## Part 5: Trademark Fundamentals

### Trademark Strategy for Tech Companies

**Priority**: Use-based vs. intent-to-use. In the US, priority is typically first-to-use (not first-to-file). Filing an ITU application secures priority from the filing date.

**Registration process**:
1. Conduct clearance search (full search: $500-$1,500; knock-out search: free via USPTO TESS)
2. File application: TEAS Standard (~$350/class) or TEAS Plus (~$250/class)
3. USPTO examination: 8-10 months to first office action
4. Publication for opposition: 30-day window for third parties to oppose
5. Statement of Use (for ITU): File within 36 months of notice of allowance
6. Registration → 10-year term, renewable indefinitely

**Common trademark mistakes**:
- **Genericness**: Names that describe the product category (e.g., "AI Assistant") can become generic
- **Descriptiveness**: Merely descriptive marks require proof of secondary meaning (5 years of use or survey evidence)
- **Failure to police**: Trademark rights require active enforcement; failure to oppose similar marks can weaken rights

### International Trademark (Madrid Protocol)
File in the US, then extend internationally via the Madrid Protocol:
- Basic fee: $653 + per-country fees (~$100-$400 per country)
- One application filed with WIPO covers 128 member countries
- Each designated country examines independently; may reject on national grounds
- Timeline: 12-18 months for initial decisions from designated countries

---

## Part 6: Technology Licensing

### Inbound License Negotiation (You Are the Licensee)

Key terms to negotiate:
1. **Scope**: What rights are granted? (use, reproduce, modify, distribute, sublicense)
2. **Exclusivity**: Exclusive vs. non-exclusive in what territory/field-of-use?
3. **IP improvements**: Who owns improvements to the licensed technology?
4. **Grant-back**: Avoid grant-backs that give the licensor rights to your improvements
5. **Audit rights**: Limit scope and frequency; negotiate reasonable advance notice
6. **Termination**: What triggers termination? Dispute resolution before termination?
7. **Source code escrow**: For critical software, negotiate escrow release conditions

### Outbound License Negotiation (You Are the Licensor)

Key protective terms:
1. **Field-of-use restrictions**: License only for specific industries or use cases
2. **Attribution requirements**: Ensure your brand is visible in products using your technology
3. **Royalty structures**: Per-unit, percentage of net revenue, or minimum annual guarantees
4. **Sublicensing restrictions**: Require written consent for sublicensing
5. **Audit rights**: Annual right to audit licensee's records with 30-day notice
6. **Performance milestones**: Require commercial launches within defined timeframes to avoid patent suppression

---

## Part 7: Employee IP and Contractor Agreements

### Critical IP Assignment Clauses

**What must be assigned**: All inventions, discoveries, developments, improvements, trade secrets, and copyrightable works that are:
- Created using company resources, time, or information
- Related to the company's current or reasonably anticipated business
- Made at any time during employment

**State law exceptions** (California, Delaware, Minnesota, Washington): Cannot require assignment of inventions that are unrelated to company business AND created entirely without company resources. Some states require employers to notify employees of these exceptions in writing.

**Moral rights**: Works of visual art have special federal moral rights (VARA) that cannot be waived by employment. For most software and business content, this is not relevant.

### Contractor vs. Employee IP
Contractors do NOT automatically assign IP to clients. Work-for-hire doctrine:
- Software created by employees within scope of employment = work-for-hire (employer owns)
- Software created by contractors = contractor retains copyright UNLESS:
  - Written agreement explicitly assigns ownership to client, OR
  - The work falls within 9 specific "specially ordered or commissioned" categories (rarely applies to software)

**Action item**: Every contractor agreement must include explicit IP assignment clause and "work made for hire" designation where applicable.

---

## Part 8: International IP Protection

### PCT (Patent Cooperation Treaty) — International Patents
PCT filing does NOT create an international patent. It:
1. Secures your priority date in 157 member countries
2. Provides a centralized examination report (ISER — International Search Report)
3. Delays national phase entry costs by 18-30 months

**Timeline**:
- File PCT within 12 months of priority application
- International publication: 18 months from priority date
- International examination (optional): 28-30 months from priority
- National phase entry deadline: 30 months from priority (31 months in some countries)

### Key Jurisdictions for Tech Companies

| Jurisdiction | Why Important | Notes |
|---|---|---|
| **United States** | Largest tech market; primary enforcement | Strong patent system; Alice challenges |
| **European Patent Office** | Efficient multi-country coverage | Software patents more restricted than US; technical character required |
| **China** | Growing importance; domestic manufacturing | China patent law reforms strengthening protection; local counsel essential |
| **Japan** | Major electronics/auto market | Software patents available; examination takes 2-4 years |
| **India** | Growing market; software IP complex | Software "per se" not patentable; technical applications allowed |

---

## Part 9: EU AI Act and IP Implications

### EU AI Act IP-Related Obligations (as of August 2025, fully effective)

**Copyright transparency** (Article 53): Providers of GPAI models must publish a "sufficiently detailed" summary of the content used for training, including a listing of main data collections and datasets.

**Implication**: If you train a model for deployment in the EU, you must be able to describe your training data sources. This requires training data provenance tracking from day one.

**Copyright compliance** (Article 53): GPAI model providers must implement policies to comply with EU copyright law, including opt-outs under the DSM Directive (Text and Data Mining exceptions).

**Content transparency**: Outputs of AI systems that generate synthetic content (images, audio, video, text) must be marked to indicate they are AI-generated (Article 50). This may affect IP status of AI outputs in the EU.

---

## Engagement Protocol

When advising on IP matters:
1. **Jurisdiction matters** — IP law varies significantly by country; always identify the applicable jurisdiction
2. **Document everything** — IP rights are established through documentation; advise on record-keeping
3. **Timeline is critical** — patent applications must be filed within strict deadlines; alert users to time pressure
4. **Flag attorney need** — complex disputes, prosecution decisions, and litigation require licensed IP attorneys
5. **Business context drives strategy** — IP strategy must align with business model (defensive vs. offensive; open source vs. proprietary)

For patent questions: assess Alice eligibility, claim scope, and prior art. For open source questions: identify specific licenses and compatibility. For trade secret questions: evaluate reasonable measures.

**Disclaimer**: This skill provides expert-level educational guidance. Specific legal advice on your situation requires consultation with a licensed IP attorney in the relevant jurisdiction.
