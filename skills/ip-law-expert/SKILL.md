---
name: ip-law-expert
description: Expert-level intellectual property law advisor covering patent fundamentals for software and AI (Alice/Mayo framework, 2025-2026 USPTO guidance), trade secrets (DTSA, reasonable measures), copyright for software and AI-generated content, trademark strategy, IP ownership in employment and contracting, AI-specific IP issues (training data, output ownership, copyright in AI systems), open-source strategy (license types, AGPL SaaS risk, SBOM requirements), DMCA compliance, IP due diligence for fundraising and M&A, licensing strategies, IP enforcement basics, and building an IP portfolio as a startup. Provides authoritative guidance through mid-2026.
---

# IP Law Expert

## Overview and Expert Mandate

Intellectual property is the primary asset of most technology companies. A startup that fails to properly protect its IP, or inadvertently infringes another's IP, faces existential risk. The IP landscape for AI/ML companies has shifted dramatically in 2025-2026 with new USPTO guidance, ongoing federal circuit decisions on AI patent eligibility, unresolved AI copyright litigation, and the emergence of SBOM requirements for open-source compliance. This skill provides expert-level IP guidance calibrated for technology and AI companies from inception through M&A or IPO.

**Disclaimer:** This skill provides expert legal education and frameworks. Consult qualified IP counsel for specific legal advice.

---

## 1. Patent Fundamentals for Software and AI

### The Four Requirements for Patentability

Every patent application must satisfy four independent statutory requirements under 35 U.S.C.:

| Requirement | Statute | Significance for AI/ML |
|-------------|---------|----------------------|
| Patentable Subject Matter | § 101 | Alice/Mayo framework — the central battleground for AI patents |
| Novelty | § 102 | Invention must not be identical to prior art |
| Non-Obviousness | § 103 | Not obvious to a person of ordinary skill (POSITA) |
| Enablement & Written Description | § 112 | Specification must teach POSITA to make and use it |

For AI/ML inventions, § 101 is the dominant obstacle. Novelty and non-obviousness are usually easier to satisfy if the architecture or training approach is genuinely new.

### The Alice/Mayo Framework (§101)

**The Two-Step Test** (*Alice Corp. v. CLS Bank*, 573 U.S. 208 (2014); *Mayo v. Prometheus*, 566 U.S. 66 (2012)):

**Step 2A, Prong One:** Is the claim directed to a judicial exception — abstract idea, law of nature, or natural phenomenon? USPTO examiners commonly characterize ML algorithms as "mathematical concepts" or "mental processes."

**2025 USPTO Change:** The August 2025 USPTO memorandum restricts overbroad "mental process" groupings — an ML algorithm processing vast datasets well beyond human cognitive capacity must NOT be categorically swept into that exclusion. Key protection for complex AI systems.

**Step 2A, Prong Two ("practical application" prong):** Does the claim integrate the exception into a practical application? Under *Enfish v. Microsoft* and *McRO v. Bandai Namco*, if claims improve the functioning of a computer or technology itself, they pass this prong.

**Step 2B:** Does the claim add "significantly more" beyond the judicial exception? Even without practical application, meaningful additional elements beyond generic computer implementation can save eligibility.

### Landmark AI Eligibility Cases (2025-2026)

**Ex Parte Desjardins (PTAB, codified December 2025):**
Most important recent AI patent precedent. Claims addressing "catastrophic forgetting" in continual learning were patent-eligible — the invention allowed AI systems to "optimize system performance, use less storage capacity, and reduce system complexity." The USPTO issued a formal memorandum integrating Desjardins into MPEP §§ 2106.04(d) and 2106.05 (December 5, 2025). Examiners now must evaluate claims under the Enfish/McRO framework rather than defaulting to abstract-idea rejections.

**Recentive Analytics v. Fox Corp. (Fed. Cir., April 2025):**
Struck down patents that "apply generic machine learning to a new data environment." Domain novelty alone is not enough. You must disclose a specific improvement to the ML system itself.

**"Do It on AI" Problem (April 2025):**
Federal Circuit confirmed that high-level functional claims like "use AI to analyze X" without specifying the technological improvement are abstract ideas — the AI equivalent of "do it on a computer" claims invalidated post-Alice.

### What Makes an AI/ML Claim Patent-Eligible in 2026

**Eligible framings (apply one or more):**
1. **Specific technical improvement to the model:** Faster convergence, reduced memory, solving catastrophic forgetting, improved gradient efficiency, novel attention mechanism
2. **Improved data structure or representation:** Novel way of encoding training data, embedding structure, or tokenization that improves computational performance
3. **Specific architectural innovation:** Not "use a neural network" but a specific novel architecture with measurable technical benefits
4. **Hardware-software integration:** Claims tied to specific hardware configurations, FPGAs, accelerator pipelines
5. **Reduction of computational complexity:** Claims that measurably reduce inference time, model size, or energy consumption

**Red flags that invite §101 rejection:**
- "Using machine learning to predict/classify [domain data]"
- Generic functional language without specifying the technical mechanism
- Claiming the output/application without claiming the technical improvement
- "Applying AI to" any business or scientific domain without specifying ML architecture changes

### Patent Claims Drafting for ML Systems

**Independent Claim Structure:**
```
A computer-implemented method comprising:
  [specific technical step 1 with concrete mechanism];
  [specific technical step 2];
  wherein [specific architectural feature] causes [measurable technical improvement];
  [hardware/memory constraint linking to improvement].
```

**Best Practices 2025-2026:**
1. **Lead with technology, not application** — "improving model training efficiency" not "predicting customer churn"
2. **Claim the architecture, not just the output** — specific neural network design, loss function modification, training procedure
3. **Quantify technical benefits in specification** — "reduces training time by 30% on equivalent hardware" — supports §101 argument
4. **Use dependent claims strategically** — Independent: broad structural improvement; Dependent: specific layer counts, activation functions, hardware configs
5. **Avoid "mental process" trap** — explicitly claim hardware execution ("a processor executes...", "in hardware memory...")

### Software Patent Prosecution Timeline and Cost

- File provisional: Day 0 (establish priority date; $320 USPTO fee + $3-8K attorney fees)
- File non-provisional (PCT or US): Month 12 ($1,820 large entity / $910 small entity / $455 micro)
- First Office Action: 18-36 months post-filing
- Patent issued: 3-5 years typically
- **Total cost per patent:** $15-25K (attorney fees + filing fees + office action responses)

**Portfolio strategy by stage:**
- Pre-seed: File provisional on core technical innovation; preserve date
- Seed: Convert 1-2 provisionals to non-provisional; establish portfolio foundation
- Series A: 3-10 patents in core technology area; defensive portfolio
- Series B+: Offensive portfolio; continuation applications on key innovations

---

## 2. Trade Secrets

### Legal Framework

**Federal:** Defend Trade Secrets Act (DTSA), 18 U.S.C. § 1836 — private right of action in federal court.
**State:** Uniform Trade Secrets Act (UTSA) adopted in 48 states (Texas, New York have own versions).

**Definition requirements:**
1. Information derives economic value from not being generally known or readily ascertainable
2. Reasonable measures to maintain secrecy (this is the operational requirement)

### What Qualifies as a Trade Secret

- Source code and algorithms (especially ML training code and hyperparameters)
- Training data and datasets not publicly available
- Model weights and architecture details
- Customer lists, pricing strategies, business plans
- Engineering processes, manufacturing methods
- Financial projections and internal metrics

### Reasonable Measures (Required to Maintain Trade Secret Status)

Courts examine whether these were in place:

**Legal measures:**
- NDA with all employees at hire (+ contractors, advisors, prospective investors)
- Invention Assignment and Confidentiality Agreement (PIIA) for employees
- Exit procedures: return of materials + reminder of obligations
- Vendor NDAs for any third-party with access to sensitive code/data

**Technical measures:**
- Access controls (need-to-know; role-based access)
- Code repository access logs
- Watermarking or fingerprinting of training data
- Encryption of sensitive files
- DLP (Data Loss Prevention) software

**Physical measures:**
- Server room access controls
- Clean desk policy for sensitive materials
- Visitor procedures (sign-in, escort, NDA)

**Labeling:**
- Mark documents "Confidential — Trade Secret" explicitly
- Source code headers with confidentiality notices
- Unlabeled materials may lose trade secret protection in court

### DTSA Enforcement

**Remedies available under DTSA:**
- Injunctive relief (stop the misappropriation immediately)
- Actual damages (value of the trade secret stolen)
- Unjust enrichment (profits from misappropriation)
- Exemplary damages (2x actual) for willful misappropriation
- Attorney's fees for willful misappropriation

**Whistleblower immunity:** DTSA includes immunity for good-faith disclosure to attorney/government. Include this notice in all NDAs to preserve exemplary damages eligibility.

### Trade Secret vs. Patent Decision

| Factor | Favor Patent | Favor Trade Secret |
|--------|-------------|-------------------|
| Duration | 20-year monopoly | Perpetual if maintained |
| Disclosure | Public disclosure required | Kept secret |
| Reverse engineering | Patent prevents it | Trade secret does not |
| Competitor independently develops | Patent still prevents | Trade secret = concurrent development loses |
| Enforceability | Clear (once granted) | Requires proving reasonable measures + misappropriation |
| Cost | $15-25K + maintenance fees | Cost of security program |
| Best for | Algorithms that can be reverse-engineered from product; major innovations worth disclosing | ML training process; proprietary data; anything that cannot be reverse-engineered from output |

---

## 3. Copyright for Software and AI

### Software Copyright

Source code is automatically protected by copyright upon creation (no registration required, but registration is required to sue for infringement).

**What is protected:**
- Literal elements: Source code, object code
- Non-literal elements: Structure, sequence, organization; creative UI elements

**What is NOT protected (Baker v. Selden rule):**
- Functional aspects, ideas, methods, algorithms as such (these require patents)
- APIs as functional specifications (Oracle v. Google — APIs themselves not copyrightable, though implementations may be)
- Facts and data

**Practical implication:** A competitor can implement the same algorithm as long as they don't copy the actual source code expression.

### Copyright in AI-Generated Content (2025-2026)

**US Copyright Office position (confirmed through 2025):**
- AI-generated content without human creative authorship is **not copyrightable**
- "Authorship" requires human creative contribution
- Prompting an AI system alone is generally insufficient to establish copyright
- Human-AI collaborative works: only the human-authored portions are protected

**What this means for your product:**
- Marketing copy, designs, code, images generated entirely by AI: no copyright protection for you
- Solution: Document human creative input at each stage; the more specific and creative the human direction, the stronger the copyright claim
- Review and substantial modification of AI outputs by humans may establish copyright

**AI training data and copyright:**
- Ongoing federal litigation: Getty Images v. Stability AI; NYT v. OpenAI; various author class actions
- Current status (mid-2026): No final appellate ruling on whether training on copyrighted content is fair use
- Fair use factors favor training at scale, but outcomes uncertain
- Practical approach: Review AI vendor terms for indemnification against training data copyright claims

### Copyright Registration

- **Why register:** Required for federal lawsuit; enables statutory damages ($750-$30,000/work, up to $150,000 for willful infringement); establishes public record
- **When to register:** Before any public release; immediately upon discovering infringement
- **Cost:** $45-65 for single work online registration; group registration for related works
- **Timely registration benefit:** If registered before infringement or within 3 months of publication, can claim statutory damages and attorney's fees (otherwise: actual damages only)

---

## 4. Trademarks and Trade Dress

### Trademark Basics

**Types of marks:**
- Word marks (brand names, product names)
- Design marks (logos)
- Trade dress (distinctive product appearance, UI design, color combinations)

**Mark strength spectrum (most → least protectable):**
Fanciful (invented words: Xerox, Kodak) > Arbitrary (existing word in new context: Apple for computers) > Suggestive (requires imagination to connect: Copilot for AI coding) > Descriptive (describes product: QuickBooks) > Generic (common name: cannot be registered)

**Use ™ immediately.** Use ® only after federal registration is granted. Using ® before registration is fraud on the USPTO and can invalidate the mark.

### USPTO Registration

**Filing requirements:**
- Mark specimen (actual use in commerce)
- Goods/services description per Nice Classification
- Filing basis: Use in commerce (1(a)) or Intent-to-Use (1(b))

**Class selection for tech/SaaS:**
- Class 42: Software as a service; cloud computing services; software development
- Class 9: Downloadable software; mobile apps
- Class 35: Business services, advertising (if applicable)
- Class 38: Telecommunications (if applicable)

**Filing fees:** $250-350 per class (TEAS Plus) + attorney fees ($2-5K for straightforward filing)

**Timeline:** 8-15 months from filing to registration for non-contested marks.

**International Registration (Madrid Protocol):**
- File one application through WIPO covering up to 130+ countries
- Base fee ~$3,500 + per-country fees
- File if you have significant revenue or plans in a market

### Trademark Monitoring and Enforcement

- Set up USPTO TEAS alerts for similar marks
- Services: TrademarkNow, CompuMark, Thomson CompuMark
- Monitor domain registrations (WHOIS alerts), social media handles, Amazon seller marks
- Demand letters for clear infringers before litigation (litigation is $500K-$2M through trial)

---

## 5. IP Ownership in Employment and Contracting

### Proprietary Information and Inventions Assignment Agreement (PIIA)

**The most important IP document for startups.** Every employee, contractor, and advisor must sign before starting work.

**Required PIIA provisions:**
1. **Assignment of inventions:** All inventions, developments, improvements created during employment are assigned to the company
2. **Scope:** Inventions related to company's business or using company resources/time (even if created on personal time)
3. **Disclosure obligation:** Employee must promptly disclose all potentially company-owned inventions
4. **License to prior inventions:** If employee wants to exclude prior inventions, they must list them in Exhibit A; unlisted prior inventions are assigned
5. **Moral rights waiver:** Employee waives any moral rights to company IP
6. **Post-employment obligation:** Assignment obligation continues for inventions that are developed using company information even after employment ends

**State limitations on invention assignment:**
- California, Delaware, Illinois, Minnesota, North Carolina, Washington: Cannot require assignment of inventions developed entirely on employee's own time without company resources, unless invention relates to company's business or is a result of employee's work
- **Practical implication:** Blanket assignments are not enforceable in these states for truly independent inventions; PIIA must include compliant carve-out language

### Contractor IP: Critical Risk Area

**Default rule:** Under copyright law, independent contractors OWN their work product unless there is a written work-for-hire agreement or assignment.

**Required in every contractor agreement:**
1. Work-for-hire clause: "All work product created under this agreement constitutes work made for hire"
2. Assignment clause (belt and suspenders): "To the extent work product does not qualify as work for hire, Contractor hereby assigns all right, title, and interest"
3. Waiver of moral rights
4. Cooperation clause: Contractor will execute additional documents to perfect company's ownership
5. Representation that work does not infringe third-party rights

**Common catastrophic failure:** Startup paid an offshore developer to build core technology with no IP assignment. At Series A, VCs discover the developer owns the IP. Deal falls apart. This has killed many fundraising processes.

### Open-Source Contributions

If employees contribute to open-source projects:
- Require employee disclosure + company approval before contributing company code
- Document which contributions are approved to release under open-source license
- Maintain record of outbound open-source contributions for M&A due diligence

---

## 6. AI-Specific IP Issues

### Copyright in AI Models

**Model weights:** Copyright protection for the specific weights is theoretically available as a computer program, but has not been definitively ruled upon in federal court. Companies should assert copyright in weights in any distribution.

**Architecture:** The neural network architecture can be patented (if novel and eligible) and copyrighted as source code. The mathematical concept itself is not protectable.

**Training data ownership:**
- Data labeled and curated by company employees: Company likely owns
- Third-party licensed data: Check license terms for permissible uses (training AI may not be permitted)
- Scraped public data: Copyright status of training data is the central issue in ongoing litigation; no clearance from fair use doctrine as of mid-2026
- Proprietary datasets as trade secrets: Often better protection than copyright for raw data

### AI Output Ownership

When your product generates AI outputs (text, images, code, designs):
- Review your AI vendor's terms of service: Many explicitly disclaim ownership and grant output rights to users
- OpenAI: Grants ownership of outputs to users (with some limitations)
- Anthropic: Users own their outputs
- Google Gemini API: Terms grant rights to use outputs

**Bottom line:** AI vendor terms generally allow you to claim ownership of AI outputs from their APIs. The copyright question (whether there IS a valid copyright in AI-generated content) is separate from who holds the rights.

### Copyright in AI Training Pipelines

Key questions your legal team must analyze:
1. What data was used to train your model?
2. Under what license or permission?
3. Were any restrictions on the licenses violated by training use?
4. Does the AI system reproduce training data verbatim? (Memorization creates infringement risk)

**Risk mitigation:**
- Maintain documentation of training data sources and licenses
- Implement deduplication and memorization testing
- Consider training on licensed/public domain data where possible
- Obtain representations and warranties from data providers

---

## 7. Open-Source Strategy for Startups

### License Categories

**Permissive (business-friendly):**
- **MIT:** Use freely; retain copyright notice; no copyleft
- **Apache 2.0:** Use freely; retain notices; patent grant (important!); must disclose modifications to the Apache-licensed portions
- **BSD 2/3-Clause:** Similar to MIT with minor variations

**Weak copyleft:**
- **LGPL (GNU Lesser GPL):** Allows dynamic linking without viral effect; modifications to the LGPL library must be released; rarely a SaaS issue

**Strong copyleft (viral):**
- **GPL v2/v3:** Distribution (including modified versions) must be licensed under GPL
- "Distribution" = shipping binary or source to third parties
- Does NOT apply to SaaS use (running GPL software on your server for internal use is fine)

**Network copyleft (critical SaaS risk):**
- **AGPL (Affero GPL):** AGPL treats network use as distribution. If users access your server running AGPL software, you must release your ENTIRE application's source code under AGPL
- This is the #1 open-source license risk for SaaS companies

**AGPL SaaS Risk Examples:**
- MongoDB Server: MongoDB released SSPL (Server-Side Public License), even broader than AGPL
- Ghostscript: AGPL — if used in a SaaS document conversion service, triggers disclosure requirement
- Some AI libraries and frameworks have AGPL versions

**AGPL mitigation options:**
1. Obtain commercial license from copyright holder
2. Rewrite the functionality
3. Use a permissively licensed alternative
4. Accept AGPL obligations (release your source code)

### Software Bill of Materials (SBOM)

SBOMs list all components in your software with license information. Now required by:
- **US Executive Order 14028** (2021): Required for all federal software procurement
- **FDA (2023):** Medical device software must include SBOM
- **EU Cyber Resilience Act (CRA):** SBOM requirement for products with digital elements
- **Enterprise customers:** Increasingly required in procurement contracts (Fortune 500)

**SBOM formats:**
- **SPDX 2.3:** Linux Foundation standard; widely adopted
- **CycloneDX 1.5:** OWASP standard; more security-focused; better tool support

**SBOM generation tools:**
- **Syft (Anchore):** Open-source; container and source scanning
- **FOSSA:** Commercial; license compliance automation; audit-ready reports
- **Black Duck (Synopsys):** Enterprise-grade; most comprehensive
- **OWASP OSS Review Toolkit:** Open-source pipeline integration

**Implementation:** Integrate SBOM generation into your CI/CD pipeline as a required gate. Generate SBOM on every release. Store in your artifact registry alongside the release.

### Contributor License Agreement (CLA)

If accepting external contributions to your codebase:
- Require CLA to ensure you can relicense contributions
- Use Apache Software Foundation CLA as template
- Individual CLA and Corporate CLA (for contributors working for employers)
- CLA Assistant (GitHub app) automates CLA enforcement

---

## 8. DMCA Compliance

### Safe Harbor Requirements (17 U.S.C. § 512)

To qualify for DMCA safe harbor (protection from copyright infringement claims for user-generated content):

1. **Designated agent:** Register a DMCA agent with the Copyright Office ($6/3-year registration); list agent on website
2. **Notice-and-takedown procedure:** Must remove infringing content promptly upon valid DMCA notice
3. **Counter-notice process:** Allow users to submit counter-notices; restore content after 10-14 days if no litigation filed
4. **Repeat infringer policy:** Terminate accounts of repeat copyright infringers
5. **No direct financial benefit from infringement:** Cannot benefit financially from infringing activity you have ability to control
6. **No actual knowledge:** Cannot have actual knowledge of infringement without acting

**Takedown response timeline:**
- Receipt of DMCA notice: Immediately acknowledge
- Evaluate validity: 24-48 hours (check required elements)
- Remove/disable access: Within 24-48 hours if valid
- Notify user: Same day as removal
- User counter-notice period: 10-14 business days
- Restore content: After counter-notice period if no litigation initiated

### AI and DMCA Issues

AI content generation creates new DMCA questions:
- If your AI reproduces verbatim copyrighted text from training data, users may receive DMCA takedowns
- AI art generators face ongoing litigation from artists claiming their works were reproduced
- Implement output filtering to detect potential verbatim reproduction of copyrighted content
- Build copyright content detection into AI pipelines for user-submitted material

---

## 9. IP Due Diligence for Fundraising and M&A

### VC Due Diligence IP Checklist

VCs at Series A and beyond will systematically review:

**Patent Portfolio:**
- Issued patents (copies of patents, prosecution histories)
- Pending applications (status, file wrappers)
- Third-party freedom-to-operate analysis for core technology
- Cross-licenses or IP agreements affecting patent ownership

**Trademarks:**
- USPTO registrations and applications
- International registrations
- Domain name ownership
- Social media handle ownership

**Copyright:**
- Software copyright registrations (if any)
- Open-source license compliance audit
- Work-for-hire documentation for contractors
- Third-party code incorporated (license permissions)

**Trade Secrets:**
- NDA/confidentiality agreements with all relevant parties
- PIIA signed by all employees and contractors
- Security procedures in place

**IP Ownership Chain:**
- Assignment from founders to company (most common diligence failure)
- Contractor work-for-hire documentation
- Any IP retained by prior employers that founder brought to company

**The Founder IP Assignment Problem:**
Most startup founders have signed Invention Assignment Agreements with their prior employers covering inventions made during or relating to their employment. If the founder:
- Used the prior employer's equipment
- Worked on ideas during prior employer's time
- Invented something in a field the prior employer was pursuing

...the prior employer may have a claim to the startup's IP. VCs will request opinion letters from IP counsel that the startup has clean chain of title.

### M&A IP Due Diligence

In M&A, buyer's counsel will examine everything above plus:
- IP litigation history (current and past)
- Third-party infringement claims
- Indemnification obligations under customer contracts
- Government rights (if any federal funding created SBIR/STTR march-in rights)
- Open-source obligations that might "infect" acquirer's products

**Clean-up before sale process:**
- 6-12 months before exit: Conduct IP audit; resolve any issues
- Register all unregistered marks
- File any unfiled applications
- Ensure PIIA signatures from all historical employees
- Resolve any open-source compliance issues

---

## 10. Licensing Strategies

### Outbound Licensing Models

| Model | Description | Best For |
|-------|-------------|---------|
| Perpetual license | One-time fee; lifetime right to use version | Legacy enterprise software |
| Subscription license | Annual/monthly fee; ongoing access | SaaS (this is your services agreement, not a license) |
| OEM/Embedded | License technology for integration into another product | APIs, SDKs, platform products |
| Open core | Core is open-source; enterprise features are commercial | Developer tools (HashiCorp, Elasticsearch model) |
| Dual license | GPL for open-source users; commercial for proprietary use | MongoDB, Qt, MySQL |
| Per-seat | Per named user or per concurrent user | Enterprise productivity tools |
| Usage-based | API calls, transactions, tokens | AI APIs, data services |

### Inbound IP Licensing

When licensing technology from third parties:
- Negotiate perpetual vs. term license (prefer perpetual for core dependencies)
- Audit rights: Right to verify compliance
- Source code access: For critical components, negotiate source code escrow
- Sublicensing rights: If you will incorporate licensed tech into your product sold to customers
- Restriction on assignment: Most licenses prohibit assignment; negotiate change-of-control carve-outs for future M&A

---

## 11. Building an IP Portfolio as a Startup

### IP Strategy Roadmap by Stage

**Pre-seed / Seed:**
1. Incorporate and have all founders assign IP to company (before writing any code for the company)
2. PIIA signed by all employees, contractors, advisors (before Day 1)
3. File trademark application for brand name + logo
4. Identify core inventions for patent protection; file provisionals
5. Conduct freedom-to-operate search for core technology

**Series A:**
1. Convert top 1-2 provisionals to non-provisionals; consider PCT for international protection
2. Open-source license audit; SBOM generation
3. Patent landscape analysis for your technology area
4. Trademark registrations in key markets (US, EU, UK, Canada)

**Series B+:**
1. Patent portfolio of 5-15 patents (mix of filed, pending, issued)
2. Trade secret program formalized (procedures, training, technical controls)
3. IP licensing agreements if technology is shared with partners
4. Competitive patent monitoring (Google Patents alerts, Innography, Derwent)
5. Freedom-to-operate analyses for new product areas

### IP Budget Planning

| Stage | Annual IP Budget | Priorities |
|-------|----------------|-----------|
| Seed | $15-30K | 2-3 provisional patent filings; trademark; PIIAs |
| Series A | $50-100K | Patent prosecution; trademark portfolio; IP audit |
| Series B | $100-250K | Patent portfolio development; international TM; litigation readiness |
| Series C+ | $250K-1M+ | Offensive portfolio; licensing program; IP operations |

---

## Quick Reference: IP Decision Matrix

| Situation | Action |
|-----------|--------|
| Founder starts company | Assign all pre-existing relevant IP to company; file 83(b) |
| First employee hire | PIIA signed before Day 1; no carve-out for relevant prior work |
| Contractor writes code | Work-for-hire + IP assignment in contract before work starts |
| Core algorithm completed | File provisional patent application within 1 year of public disclosure |
| Brand name chosen | Check USPTO; file trademark application (Class 42 for SaaS) |
| Using AGPL library in SaaS | Obtain commercial license OR remove OR accept AGPL obligations |
| AI generates your content | Review vendor ToS for output rights; document human creative input |
| Employee leaves to compete | Exit interview; remind of NDA/PIIA; revoke access same day |
| Fundraising upcoming | IP audit: clean chain of title; resolve open-source issues |
| M&A target evaluation | Verify PIIA chain; freedom-to-operate; open-source compliance |
