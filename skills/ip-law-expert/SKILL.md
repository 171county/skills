---
name: ip-law-expert
description: >
  Expert IP law advisor for tech startups, AI companies, and software businesses. Invoke when
  the user asks about: patents (software patents, AI/ML patents, provisional applications, PCT
  filings, Alice/Mayo eligibility), copyright (code copyright, AI training data, AI-generated
  outputs, DMCA), trade secrets (protection programs, NDAs, employee agreements), trademarks
  (registration, domain names, international marks), open source licensing (GPL/AGPL/MIT/Apache
  risks and strategy), AI-specific IP issues (training data rights, model ownership, output
  copyright), IP due diligence (FTO analysis, M&A IP audits), employee IP agreements (PIIAs,
  invention assignment, non-competes by state), licensing models (royalties, cross-licensing,
  FRAND, patent pools), international IP (EU AI Act, China strategy, Madrid Protocol), IP
  litigation (cease and desist, DMCA takedowns, patent trolls/NPEs, IPR proceedings), or open
  source compliance (SBOM, license scanning, copyleft contamination). Also invoke for IP
  strategy by funding stage, common startup IP mistakes, and VC/M&A IP preparation.
---

# IP Law Expert for Tech Startups and AI Companies

> DISCLAIMER: Everything in this skill is informational and educational only. It does not constitute legal advice and does not create an attorney-client relationship. IP law is highly fact-specific and jurisdiction-dependent. Always consult a licensed attorney for advice on your specific situation before taking legal action or making significant IP decisions.

---

## How to Use This Skill

You are acting as a senior IP attorney with 15+ years advising tech startups, AI companies, and software businesses. Deliver actionable, expert-level guidance. Flag when a situation genuinely requires retained counsel. Reference current case law and regulatory developments where relevant. Never guess on highly jurisdiction-specific questions — recommend appropriate specialized counsel instead.

When a user asks an IP question:
1. Identify the IP category (patent / copyright / trade secret / trademark / open source / AI-specific)
2. Identify the company stage and context
3. Deliver structured, prioritized guidance
4. Flag immediate action items and long-horizon strategy separately
5. Always close with the disclaimer to consult a licensed attorney for their specific situation

---

## IP Strategy Framework by Company Stage

### Pre-Seed / Idea Stage (0–6 months, pre-funding)

**Priorities:** Lock down ownership, establish confidentiality, avoid public disclosure traps.

- **Founder IP assignment**: Every founder must sign an IP assignment agreement before receiving equity. Work product created before incorporation must be explicitly assigned. This is the single most common fatal flaw found in due diligence.
- **Incorporate immediately**: IP belongs to humans until it is assigned to an entity. Incorporate (Delaware C-Corp for US VC-backed startups) and run all work through the entity.
- **Contractor agreements**: Every freelancer, agency, and consultant must sign a written agreement containing both a "work made for hire" clause (copyright) AND an explicit assignment of all inventions and IP (patents). Work-made-for-hire alone does not transfer patent rights.
- **NDAs before disclosure**: Use mutual NDAs before sharing any technical details with potential co-founders, advisors, or partners. Note: NDAs are nearly impossible to enforce in Silicon Valley's informal culture, so treat them as a formality and be conservative about what you share verbally.
- **No public disclosure yet**: Do not publish, blog, present at conferences, or post on GitHub before at least filing a provisional patent application if patent protection is contemplated. The US has a 1-year grace period from public disclosure; almost all other countries have zero grace period (absolute novelty requirement).
- **Trade secret baseline**: Document your core technology in internal memos timestamped in secure systems. Begin access controls. Mark sensitive documents "Confidential."

**Cost**: $2,000–$8,000 in basic legal setup (incorporation, founder agreements, NDA templates). Spend this money.

---

### Seed Stage ($500K–$3M raised)

**Priorities:** File provisional patents, formalize trade secret program, nail trademark, get PIIA in place for all employees.

- **Provisional patent applications**: File provisionals on your 2–3 most novel technical innovations. Cost: $1,500–$4,000 per provisional with a good patent attorney. This buys 12 months to test the market before committing to a full non-provisional (~$8,000–$25,000). Use rolling provisionals for evolving technology.
- **PIIA for all employees**: Every employee must sign a Proprietary Information and Inventions Agreement (PIIA) at offer stage, before their start date. Do not let anyone start working without it. Include: (a) assignment of all inventions related to company business, (b) confidentiality obligations, (c) prior inventions schedule (to carve out pre-existing personal IP so there is no later dispute).
- **Open source audit**: Conduct your first open source license audit. If you have any GPL/AGPL dependencies in your proprietary codebase, address them now before the codebase grows.
- **Trademark search and filing**: Before you launch publicly, run a clearance search for your brand name and logo. File a US federal trademark application ($250–$350 per class, per mark). Do not build a brand on an unregistered mark.
- **Domain name strategy**: Register .com, .ai, and .io variants of your brand name. File trademark before acquiring domain names to avoid paying extortion-level prices from domain squatters who monitor trademark filings.
- **Trade secret policy**: Implement a formal trade secret protection program. Access controls, need-to-know documentation, clear labeling, employee training.

---

### Series A ($5M–$20M raised)

**Priorities:** Convert provisionals to non-provisionals, begin building a patent portfolio, implement open source compliance program, begin international IP.

- **Convert provisionals**: Decide which provisional applications to convert to full non-provisional applications. Non-provisional applications are expensive ($8,000–$25,000) — be selective. Convert only those covering core, defensible technical advantages.
- **PCT filing**: If you have international market ambitions, file a PCT application within 12 months of your provisional (or non-provisional) to preserve rights in 150+ countries. The PCT buys you 30 months from priority date before expensive national phase filings. Cost: $3,000–$6,000 at PCT stage; national phase adds $5,000–$15,000+ per country.
- **Patent portfolio strategy**: A "picket fence" strategy — filing multiple patents around a core technology — is more defensible than a single broad patent. Aim for 5–15 patents covering the core technology and adjacent improvements.
- **Freedom to Operate (FTO) analysis**: Before major product launch or fundraising, commission an FTO analysis to confirm your product does not infringe existing third-party patents. An FTO costs $5,000–$20,000 but is far cheaper than an infringement lawsuit.
- **Open source compliance program**: Implement SBOM (Software Bill of Materials) tooling (see Open Source section). Establish a policy prohibiting AGPL usage without legal review.
- **International trademark**: If you are expanding to Europe, file an EU trademark (EUIPO, ~€850 for one class). If broader international reach, use the Madrid Protocol for cost-efficient multi-country filing.
- **Investor data room**: Organize your IP for due diligence: patent applications (with filing dates and status), trademark registrations, key employee PIIAs, open source compliance documentation, any third-party IP licenses you rely on.

---

### Series B and Beyond / Pre-IPO

**Priorities:** Maximize portfolio value, address litigation exposure, EU AI Act compliance, M&A readiness.

- **IP audit**: Commission a full IP audit to identify gaps, value the portfolio, and clean up title issues before M&A or IPO.
- **Defensive patent strategy**: Join the LOT Network (free protection against patent assertion entities that hold LOT patents). Consider Unified Patents membership to defend against NPEs in your technology area.
- **Cross-licensing**: Establish cross-licensing relationships with strategic partners and non-competitive peers.
- **EU AI Act compliance**: If you deploy AI systems in the EU, conduct an EU AI Act compliance assessment. GPAI model providers must maintain technical documentation and publish a copyright compliance policy for training data.
- **Litigation readiness**: Retain a patent litigation firm on a watch-list basis. Maintain an "IP litigation reserve" in your financial planning.

---

## Patent Law: Deep Dive

### Software Patent Eligibility (Alice/Mayo Framework)

Software patents in the US face a two-step eligibility test under *Alice Corp. v. CLS Bank* (2014):

**Step 1**: Is the claim directed to a judicial exception (abstract idea, law of nature, natural phenomenon)?

**Step 2 (if yes)**: Does the claim amount to "significantly more" than the abstract idea — i.e., does it recite an inventive concept that transforms the abstract idea into a patent-eligible application?

**How to draft software patents that survive Alice:**
- Claim specific technical improvements to computer functionality — not merely "doing X on a computer"
- Tie claims to specific hardware architectures, data structures, or technical processes
- Describe the invention in terms of solving a *technical problem* with a *technical solution*
- Avoid purely functional claiming (e.g., "a means for optimizing X")
- The 2024 USPTO AI Subject Matter Eligibility Guidance (Examples 47–49) provides specific roadmaps for AI/ML patent claims
- Good targets: novel neural network architectures, specific training methods, novel inference optimization techniques, hardware-software co-designs, specific data processing pipelines with technical improvements

**What doesn't work:**
- Applying a known algorithm to a new field
- Collecting and displaying data without a technical improvement
- Pure business method automation

### AI/ML Patent Strategy

**What is patentable:**
- Novel neural network architectures (specific structural innovations)
- Training methods with measurable technical improvements (speed, efficiency, accuracy via a specific technical mechanism)
- Hardware-software co-designs
- Novel inference optimization techniques
- Specific data pre-processing pipelines with technical novelty
- Novel fine-tuning or adaptation methods

**Inventorship in AI-assisted inventions (2025 USPTO guidance):**
- Only humans can be listed as inventors — the Supreme Court declined to review *Thaler v. Perlmutter* in March 2026, leaving the human-authorship requirement intact
- AI can be used as a tool, analogous to a laboratory instrument, without disqualifying human inventors
- The 2025 revised USPTO guidance explicitly states that using AI tools in the inventive process does not forfeit human inventorship status — but at least one human must have made a significant contribution to the conception of each claimed invention
- Inventorship risk: if human contributors are improperly omitted or non-contributors listed, the patent can be invalidated

**Prior art search**: Commission a prior art search before drafting claims. WIPO documented 480,000+ AI patent publications between 2019 and 2024. The AI patent landscape is dense.

### Patent Filing Decision Tree

```
Is the innovation genuinely novel and non-obvious?
├── NO → Don't file. Consider trade secret instead.
└── YES → Will you publicly disclose within 12 months?
    ├── YES (US only) → File provisional before disclosure
    └── YES (international) → File provisional immediately (no grace period abroad)
        └── Is this a core competitive differentiator?
            ├── NO → Consider trade secret; provisional buys time to evaluate
            └── YES → File provisional now
                └── Do you have international market ambitions?
                    ├── YES → File PCT within 12 months of provisional
                    └── NO → Convert to US non-provisional within 12 months
                        └── Priority countries: US first, then EU, China, Japan
                            based on where your market and competitors are
```

**Provisional patent best practices:**
- File provisionals rolling as you develop new features — each gets its own priority date
- A provisional must have enough detail to support the claims you will file in the non-provisional (inadequate provisionals provide no protection)
- Provisionals expire after 12 months with no extensions; you must convert or you lose the priority date
- Do not publish, present, or launch before filing

### PCT International Strategy

The Patent Cooperation Treaty covers 150+ countries with a single application:

| Stage | Timing | Cost | Purpose |
|-------|--------|------|---------|
| PCT filing | Within 12 months of priority date | $3,000–$6,000 | Preserves international rights |
| International Search Report | ~16 months after priority | Included | Prior art assessment |
| Publication | 18 months after priority | — | Application becomes public |
| Optional IPER | ~22 months after priority | $1,000–$1,500 | Preliminary patentability opinion |
| National phase entry | 30 months after priority | $5,000–$15,000+ per country | Actual country filings |

**Country selection strategy:**
- US: Always
- EU (EPO): If you have European market or competitors
- China (CNIPA): If you have China market, manufacturing, or Chinese competitors (filing locally is essential — China uses first-to-file)
- Japan, South Korea: If significant markets or competitors
- India: Rapidly growing tech market, increasingly important

---

## Copyright: Software and AI

### Software Copyright Basics

- Software code is protected by copyright from the moment of creation — no registration required for protection
- Copyright registration with the US Copyright Office ($35–$65) is required *before* you can sue for infringement in US federal court, and registration within 3 months of publication or before infringement enables statutory damages ($750–$150,000 per work) and attorney's fees — worth doing for your core codebase
- Copyright in software covers: source code, object code, documentation, UI elements (to extent creative), database selection/arrangement
- Copyright does NOT protect: APIs (functionality), ideas, algorithms, systems (only the expression of them)
- The Oracle v. Google saga (2021 Supreme Court) held that Google's use of Java APIs in Android was fair use — but the underlying question of whether APIs are copyrightable remains contested and fact-specific

### AI Training Data Copyright — Current State of the Law (2025–2026)

This is the most rapidly evolving area of IP law. Here is the current state:

**Key rulings:**

*Bartz v. Anthropic* (N.D. Cal., June 23, 2025): Using *legally acquired* books to train LLMs was held "spectacularly transformative" and protected as fair use. However, creating a permanent library of *pirated* books was NOT fair use. Anthropic later settled for $1.5 billion (the largest copyright settlement in US history), covering approximately 500,000 books at roughly $3,000 per book, and agreed to destroy its pirated dataset.

*Kadrey v. Meta* (N.D. Cal., June 25, 2025): Training Meta's LLMs on books was "highly transformative" and fair use. This court went further than Bartz by also excusing Meta's use of shadow library copies.

*Thomson Reuters v. ROSS Intelligence* (D. Del., February 11, 2025): ROSS's use of Thomson Reuters headnotes to train a legal research AI was NOT fair use. The court held the headnotes were protectable and ROSS's defense failed because ROSS's product directly competed with Westlaw.

**Key takeaway for AI companies:** Fair use for AI training is NOT a blanket defense. Courts apply a four-factor analysis. Critical factors:
1. **Transformativeness**: Is the purpose of training fundamentally different from the expressive purpose of the original? (Higher for general LLMs; lower for task-specific models that compete with source content)
2. **Nature of the work**: Published works vs. unpublished; factual vs. highly creative
3. **Amount used**: Are entire works ingested or representative samples?
4. **Market harm**: Does the AI output compete with or substitute for the original market? (The ROSS case turned on this)

**Practical risk tiers for training data:**

| Data Source | Risk Level | Recommended Approach |
|-------------|------------|----------------------|
| Licensed data (paid API, data licensing agreements) | Low | Document license terms carefully |
| Public domain / CC0 works | Very Low | Ideal — use freely |
| Creative Commons licensed works | Low–Medium | Check specific license; CC-BY/CC-BY-SA OK; NC licenses may prohibit commercial use |
| Scraped web data (Common Crawl) | Medium | Opt-out requests are accumulating; honor robots.txt; filter copyrighted content |
| Books, music, code without license | High | Bartz suggests fair use possible if not pirated, but litigation risk is substantial |
| Shadow library / pirated copies | Very High | Bartz explicitly held NOT fair use; avoid entirely |
| Competitors' proprietary data | Extreme | Do not use |

**EU AI Act training data requirements (for GPAI models):**
- GPAI model providers must implement a copyright policy
- Must publish a detailed summary of training content (transparency requirement)
- Must honor EU copyright opt-out mechanisms (Article 4(3) DSM Directive)
- A centralized European opt-out register is proposed; monitor for implementation

### AI-Generated Output Copyright

**US position (settled as of March 2026):**
- The Supreme Court declined to review *Thaler v. Perlmutter* (March 2026), leaving intact the DC Circuit's ruling that purely AI-generated works cannot be copyrighted — human authorship is required
- Works with "sufficient human creative control" over the expressive elements can be copyrighted
- Prompt engineering alone (text input to generate image/text) is currently NOT enough for copyright
- Human selection, arrangement, and creative modification of AI outputs can create copyrightable elements
- When registering AI-assisted works: disclose AI contributions; claim only human-authored elements

**Practical guidance:**
- Document human creative decisions in your AI-assisted workflows
- The more human selection, arrangement, judgment, and modification involved, the stronger your copyright claim
- AI-generated code lacks copyright protection — this has implications for code generation tools and whether the output is truly "owned" by the user
- Maintain human review and modification in creative workflows if copyright matters for your business model

### DMCA Takedowns

The Digital Millennium Copyright Act (DMCA) provides a notice-and-takedown system for online infringement:

**Sending a DMCA takedown:**
1. Identify the infringing content and the platform/host
2. Draft a compliant notice: (a) identification of the copyrighted work; (b) identification of the infringing material with URL; (c) your contact information; (d) good faith statement; (e) accuracy statement; (f) signature
3. Send to the platform's registered DMCA agent (searchable at copyright.gov/dmca-directory)
4. Platform must remove content "expeditiously" or lose safe harbor
5. Counternotice process: if the other party files a counternotice, content can be restored after 10–14 days unless you file suit

**Warning:** Filing a knowing misrepresentation in a DMCA takedown exposes you to liability under 17 U.S.C. § 512(f). Ensure you actually own the copyright before filing.

---

## Trade Secrets: Protection Strategy

### What Is a Trade Secret?

Under the Defend Trade Secrets Act (DTSA, 2016, federal) and state trade secret laws (most states have adopted the Uniform Trade Secrets Act):

A trade secret is any information that:
1. Derives independent economic value from being secret, AND
2. Is subject to reasonable measures to maintain its secrecy

**What qualifies:** algorithms, model weights and architectures, training datasets, source code (especially AI pipeline code), customer lists, pricing strategies, business methods, roadmaps, financial models, hardware designs.

**Critical rule:** If you do not take affirmative steps to protect secrecy, you cannot claim trade secret protection. Courts look at whether you treated the information as a secret.

### Trade Secret Protection Checklist

**Access controls:**
- [ ] Document which employees have access to what information (need-to-know basis)
- [ ] Use role-based access controls in your code repositories (e.g., separate repos for core AI training code vs. application layer)
- [ ] Require authentication and log access to sensitive systems
- [ ] Separate critical IP from general employee access

**Documentation and labeling:**
- [ ] Mark all sensitive documents "CONFIDENTIAL — TRADE SECRET"
- [ ] Include confidentiality notices in code file headers for proprietary algorithms
- [ ] Timestamp all technical documentation (use version-controlled repositories with commit history)
- [ ] Document the trade secret inventory in a privileged memorandum to counsel

**Legal agreements:**
- [ ] All employees sign PIIA with confidentiality provisions (before first day of work)
- [ ] All contractors/vendors sign NDA before any technical disclosure
- [ ] Mutual NDAs with potential partners, acquirors, investors who receive sensitive technical information
- [ ] NDA with investors: VCs rarely sign NDAs for pitch meetings; use NDAs only when sharing actual technical details in diligence

**Employee lifecycle:**
- [ ] Conduct exit interviews covering trade secret obligations
- [ ] Revoke access to all systems on the employee's last day (preferably the moment they give notice)
- [ ] Send a departure reminder letter outlining ongoing confidentiality obligations
- [ ] If a departing employee is joining a direct competitor, consult counsel immediately about protective measures

**Physical security:**
- [ ] Visitor policy: do not allow visitors into technical areas without escort
- [ ] Clean desk policy for sensitive work
- [ ] Screen-lock policies and device encryption

**Technical security:**
- [ ] Encrypt sensitive data at rest and in transit
- [ ] Monitor for unusual data exfiltration (large downloads, external transfers)
- [ ] Use DLP (Data Loss Prevention) tools for sensitive IP

### NDAs: Key Provisions

A well-drafted NDA for technical IP should include:
- **Definition of Confidential Information**: Broad enough to cover oral disclosures (which must be confirmed in writing within 30 days) and embedded in documents
- **Standard of care**: "At least the same degree of care as recipient uses to protect its own confidential information, but no less than reasonable care"
- **Permitted disclosure**: Limited to those employees with a need to know, who are themselves bound by confidentiality
- **Exclusions**: Information that is (a) already public, (b) known to recipient before disclosure, (c) independently developed, (d) received from a third party without restriction — these are standard and should not be contested
- **Term of confidentiality**: For technical trade secrets, push for indefinite confidentiality (or at least 5–10 years) rather than the standard 2-year NDA term
- **Return/destruction of materials**: Upon termination, require return or certified destruction of confidential materials
- **Injunctive relief provision**: State that breach would cause irreparable harm and that injunctive relief is appropriate — this speeds up emergency court proceedings
- **Governing law**: Specify jurisdiction (Delaware or your state of incorporation is typical for US companies)

---

## Trademark: Brand Protection

### Trademark Basics for Tech Startups

A trademark protects source identifiers: names, logos, slogans, and other brand elements that distinguish your goods/services from competitors. Rights arise from use, but federal registration provides critical advantages.

**Before you name your company or product:**
1. Run a USPTO TESS search (tmsearch.uspto.gov) for identical and confusingly similar marks in your relevant classes
2. Run a Google search and check domain availability
3. Run international searches if you intend global launch
4. Consider hiring an IP attorney for a formal clearance opinion for significant brand investments
5. Social media handle availability check

**Key trademark classes for tech:**
- Class 9: Software, downloadable apps, electronic devices
- Class 38: Telecommunications, communication services, internet access
- Class 42: Software as a Service, cloud computing, AI-as-a-service, research and development
- Class 35: Business services, online marketplace

**Registration timeline and costs:**
- USPTO filing: $250–$350 per class (TEAS Plus) or $350+ (TEAS Standard)
- Examination: ~3–6 months to first office action
- Registration: typically 8–18 months total if no opposition
- Maintenance: file Section 8 declaration between years 5–6, and Section 15 declaration; then every 10 years

**Common rejection grounds:**
- Likelihood of confusion with existing mark (the most common)
- Merely descriptive or generic marks (e.g., "CloudStore" for cloud storage)
- Primarily merely a surname
- Geographic descriptiveness

**Domain names:** A domain name does not confer trademark rights. If someone registers a domain that is confusingly similar to your registered trademark, you can file a UDRP (Uniform Domain Name Dispute Resolution Policy) complaint with WIPO — far cheaper than litigation ($1,500–$5,000 vs. six figures). Requires showing: (a) your mark is identical or confusingly similar to the domain, (b) the registrant has no legitimate interest, (c) the domain was registered and used in bad faith.

### International Trademark: Madrid Protocol

The Madrid Protocol allows filing in 132 countries via a single WIPO application based on your home country application or registration.

**Cost structure:**
- Basic fee: ~CHF 653 (approximately $730 USD)
- Country fees: varies by country (CHF 20–1,000+ per country)
- Typical 10-country filing: $3,000–$7,000 vs. $20,000+ filing directly in each country

**Strategic use:**
- File Madrid extension after your US trademark is filed (can be based on a pending application)
- Priority markets for tech startups: EU (EUIPO, covers 27 countries as one designation), UK, Canada, Australia, Japan
- China: File separately in China via direct application AND through Madrid — Chinese trademark law is first-to-file and notorious for bad-faith pre-registration by locals

**Central attack risk:** If your home country (US) application is refused or cancelled within the first 5 years, all Madrid designations fall. Ensure your home mark is on solid ground.

---

## Open Source Licensing

### License Categories and Risk Profile

**Permissive Licenses (low risk for proprietary products):**

| License | Patent Grant | Attribution | Can Use in Proprietary Product |
|---------|-------------|-------------|-------------------------------|
| MIT | No | Yes (in notices) | Yes |
| BSD 2-Clause | No | Yes | Yes |
| BSD 3-Clause | No | Yes | Yes |
| Apache 2.0 | Yes | Yes | Yes |
| ISC | No | Yes | Yes |

Apache 2.0 is generally superior to MIT for commercial use because it includes an explicit patent grant — the patent holder grants users a license under any patents the contributor holds that are necessarily infringed by their contribution.

**Weak Copyleft (medium risk — component-level obligation):**

| License | Trigger | Obligation |
|---------|---------|------------|
| LGPL v2.1 / v3 | Linking/modification of the LGPL component | Must allow relinking; modifications to LGPL file itself must be released |
| MPL 2.0 | Modification of MPL files | Only modified MPL files must be open-sourced ("file-level" copyleft) |
| Eclipse Public License 2.0 | Modification | Module-level copyleft; compatible with GPL v2+ |

LGPL is commonly used for libraries (e.g., certain GNU C Library components, FFmpeg). Using an LGPL library via standard dynamic linking is generally acceptable in a proprietary product, but modifying LGPL code or static linking may trigger disclosure obligations.

**Strong Copyleft (high risk — program-level obligation):**

| License | Trigger | Obligation |
|---------|---------|------------|
| GPL v2 | Distribution of software incorporating GPL code | Full source of combined program must be made available |
| GPL v3 | Distribution of software incorporating GPL code | Full source + anti-tivoization; additional restrictions on DRM and patents |
| AGPL v3 | Distribution OR network interaction (SaaS trigger) | Full source of combined program, including your proprietary code, must be available |

**AGPL is the most dangerous license for SaaS startups.** The "network use" trigger means that if users interact with AGPL-covered software over a network (i.e., your web application uses AGPL code), you must make the full source code of your application available. This can effectively require open-sourcing your entire product. Google's policy is to prohibit AGPL usage internally — this is a sensible model for most commercial companies.

**Copyleft contamination mechanism:**
AGPL or GPL contamination is not limited to direct dependencies. A library that depends on a GPL/AGPL library can transmit the copyleft obligation. One AGPL component buried three dependencies deep can contaminate your codebase. This is why automated license scanning is essential.

### Open Source License Compatibility Matrix

```
Can I combine A (column) into a project licensed as B (row)?

         MIT  Apache2  LGPL  MPL2  GPL2  GPL3  AGPL3
MIT       ✓     ✓       ✓     ✓     ✓     ✓      ✓
Apache2   ✓     ✓       ✓     ✓     ✗     ✓      ✓
LGPL      ✓     ✓       ✓     ✓     ✓     ✓      ✓
MPL2      ✓     ✓       ✓     ✓     ✓     ✓      ✓
GPL2      ✓     ✗       ✓     ✓     ✓     ✗      ✗
GPL3      ✓     ✓       ✓     ✓     ✓     ✓      ✓
AGPL3     ✓     ✓       ✓     ✓     ✓     ✓      ✓

✓ = Compatible (output can be licensed under B)
✗ = Incompatible

Key incompatibilities:
- Apache 2.0 is incompatible with GPL v2 (patent termination clause conflict)
- GPL v2 is incompatible with GPL v3 (cannot combine v2-only with v3)
- If your project output is proprietary: avoid GPL and AGPL components entirely
```

### Open Source Compliance Program

**SBOM (Software Bill of Materials):**
A SBOM is a complete inventory of all software components, their licenses, and versions in your codebase. It is rapidly becoming a requirement for enterprise sales, government contracts, and M&A due diligence.

**SBOM standards:**
- **SPDX**: Linux Foundation standard; most widely used
- **CycloneDX**: OWASP standard; stronger security/vulnerability focus
- **SWID**: NIST standard; enterprise asset management

**Recommended tooling for startups:**

| Stage | Tools | Cost |
|-------|-------|------|
| Pre-Series A | Syft + Trivy (open source) | Free |
| Series A–B | FOSSA, Snyk, or Black Duck | $500–$5,000/month |
| Enterprise/M&A prep | Black Duck, FOSSA Enterprise | Custom |

**Practical compliance checklist:**
- [ ] Run license scan on entire codebase quarterly (automated in CI/CD)
- [ ] Establish a written open source policy: prohibited licenses (AGPL, SSPL, Commons Clause), approved licenses (MIT, Apache 2.0, BSD, ISC), and review-required licenses (LGPL, MPL, GPL)
- [ ] Document all open source usage in an SBOM
- [ ] Provide required attribution for permissive licenses (include NOTICES file or equivalent)
- [ ] For any GPL/LGPL code used: document the linking method and whether source disclosure obligations are triggered
- [ ] Assign an "open source steward" — an engineer or counsel who reviews and approves new open source component introductions
- [ ] Review SBOM before M&A data room access is granted

**When GPL contamination is discovered:**
1. Do not panic — but do not ignore it
2. Options: (a) remove the GPL component and replace with a permissively-licensed alternative, (b) obtain a commercial license from the component's licensor (many popular GPL-licensed tools have commercial dual-licensing), (c) isolate the GPL component in a separate process/service with a clean API boundary, (d) if you distributed already, assess your disclosure obligations with counsel
3. Do not proceed with fundraising or M&A while known GPL contamination is unresolved

---

## Employee IP Agreements: Key Provisions

### The PIIA (Proprietary Information and Inventions Agreement)

The PIIA is the foundational IP document for every employee and contractor. Every person who touches your product must sign one before their first day. This is non-negotiable.

**Core provisions:**

**1. Assignment of Inventions:**
Employee assigns to the company all inventions, developments, improvements, and works of authorship made, conceived, or reduced to practice during employment that (a) relate to the company's business, (b) result from work performed for the company, or (c) use company equipment, supplies, facilities, or confidential information. This should cover both patentable inventions and copyrightable works.

**2. State law carve-outs (mandatory — these CANNOT be contracted around):**
The following states require carve-outs for inventions made entirely on personal time, with no company resources, unrelated to company business:
- California (Labor Code § 2870)
- Washington (RCW 49.44.140)
- Minnesota (Minn. Stat. § 181.78)
- Delaware (Del. Code tit. 19, § 805)
- Illinois (765 ILCS 1060/2)
- North Carolina (N.C. Gen. Stat. § 66-57.1)
- Kansas (K.S.A. § 44-130)
- Utah (Utah Code § 34-39-3)

Failing to include these carve-outs can invalidate the entire assignment clause in these states.

**3. Prior Inventions Schedule:**
Employee lists all inventions, developments, or works they own or have rights to BEFORE employment that they wish to exclude from assignment. Employees with nothing to list sign a "nothing to disclose" statement. This prevents later disputes where a departing employee claims company IP was actually their pre-existing work.

**4. Confidentiality:**
Employee agrees to maintain confidentiality of all Proprietary Information during and after employment (typically indefinitely for trade secrets, 2–5 years for other confidential information). Define Proprietary Information broadly to include: technical data, trade secrets, know-how, research, product plans, services, customers, markets, software, computer programs, code, financial information, business projections.

**5. Non-solicitation of employees:**
Prohibition on recruiting company employees for 12–24 months post-departure. This is enforceable in most states (unlike non-competes).

**6. Non-solicitation of customers:**
Prohibition on soliciting known customers for 12 months post-departure. Enforceability varies by state.

**7. Return of materials:**
All company property — devices, documents, code, data — must be returned on the last day.

**8. Work made for hire:**
To the extent any work product is not automatically owned by the company under employment law, employee agrees it constitutes work made for hire; to the extent it does not qualify as work made for hire by operation of law, employee assigns all rights.

**Contractor version (critically important):**
Work-made-for-hire under copyright law requires either: (a) employment relationship, or (b) a written agreement designating the work as work made for hire AND the work falling into one of 9 enumerated statutory categories. Software development generally does NOT fall into those categories — so for contractors, you MUST have an explicit assignment clause. Many startups learn this the hard way when a contractor asserts ownership of critical code years later.

### Non-Compete Agreements (2025 State of the Law)

The FTC's nationwide ban on non-competes (2024) was struck down by a Texas federal court and the FTC withdrew its appeal in September 2025. Non-competes are now governed entirely by state law.

**State-by-state summary (tech startups):**

| State | Status | Notes |
|-------|--------|-------|
| California | Void and unenforceable (near-total ban) | Except for sale of business; even choice-of-law clauses selecting another state are often disregarded |
| Minnesota | Void and unenforceable | Banned as of 2023 |
| North Dakota | Void and unenforceable | Near-total ban |
| Oklahoma | Void and unenforceable | Near-total ban |
| Washington | Enforceable above ~$123,000 salary threshold | Must be reasonable in scope |
| Colorado | Enforceable above ~$127,000 for "highly compensated" employees | Must protect legitimate business interests |
| Illinois | Enforceable above $75,000 | Must be reasonable |
| New York | Enforced when protecting legitimate business interests, reasonable in scope | Recent reform proposals pending |
| Texas | Generally enforceable if reasonable | Must be ancillary to an otherwise enforceable agreement; must include geographic, time, and scope limitations |
| Florida | Generally enforced; employer-friendly | Statutory presumption in favor of enforcement |
| Delaware | Enforced with reasonableness review | |

**For California-based startups:** Do not rely on non-competes. California courts will often refuse to apply a non-California choice-of-law clause in a non-compete where the employee lives and works in California. Focus protection on trade secret agreements, NDAs, and non-solicitation provisions (which ARE enforceable in California).

**Best practice in all states:** Even where non-competes are valid, focus on the trade secret provisions of your PIIA — these are more broadly enforceable and more practically effective for protecting genuinely confidential technical information.

---

## IP Due Diligence Checklists

### For Raising a Round (Investor Due Diligence)

Prepare this data room before a Series A or later:

**Ownership and chain of title:**
- [ ] Certificate of Incorporation and any IP-related provisions in charter documents
- [ ] Signed PIIAs for all current and former employees (especially founders)
- [ ] Signed contractor/consultant agreements with IP assignment provisions
- [ ] Founder IP assignment agreements (separate from PIIA if IP was created before incorporation)
- [ ] Confirmation that all university/prior employer IP is properly handled (no "taint" from prior employer IP)
- [ ] List of any IP licenses granted to or received from third parties

**Patent portfolio:**
- [ ] List of all patent applications (provisional and non-provisional) with filing dates, serial numbers, status
- [ ] List of any patents granted (patent numbers, issue dates, maintenance fee status)
- [ ] Any pending office actions and responses
- [ ] FTO clearance analysis (if conducted)
- [ ] Any third-party patent claims or licensing demands received

**Copyright:**
- [ ] Software copyright registrations (if any)
- [ ] Open source license compliance documentation
- [ ] SBOM or open source inventory
- [ ] Third-party software licenses (commercial licenses relied upon)

**Trademarks:**
- [ ] Trademark registrations and pending applications (US and international)
- [ ] Domain name registrations
- [ ] Any trademark conflicts or opposition proceedings

**Trade secrets:**
- [ ] Trade secret protection policy and program documentation
- [ ] Description of what is treated as a trade secret
- [ ] Any actual or suspected trade secret misappropriation incidents

**AI-specific (for AI companies):**
- [ ] Training data provenance documentation and licenses
- [ ] Documentation of data filtering and opt-out compliance
- [ ] Any copyright demands related to training data

### Freedom to Operate (FTO) Analysis

An FTO analysis determines whether you can commercialize your product without infringing valid, enforceable third-party patents.

**FTO process:**
1. **Define the product**: Create a detailed technical description of all features and implementation details
2. **Identify relevant patent classes**: Work with patent counsel to identify relevant technology classifications (CPC/IPC codes)
3. **Search**: Comprehensive patent database search (USPTO, EPO, WIPO, CNIPA) for patents that might cover your product
4. **Claim analysis**: For each potentially relevant patent, analyze whether your product falls within the scope of any claim (both literal infringement and doctrine of equivalents)
5. **Validity assessment**: For patents that read on your product, assess whether they are likely valid (prior art challenges, obviousness)
6. **Map and prioritize**: Rank patents by risk level
7. **Mitigation options**: For high-risk patents: design around, seek a license, file IPR/inter partes review if the patent is weak

**When to do an FTO:**
- Before major product launch
- Before Series A or significant fundraising round
- When entering a new technology area or product line
- Before M&A (both sides)
- When you receive a patent demand letter

**FTO costs:** $5,000–$20,000 for a standard analysis; $20,000–$100,000+ for complex multi-patent/multi-product analyses. Still far cheaper than patent litigation (average US patent trial: $3M+).

### M&A IP Diligence (Acquiror Checklist)

When acquiring a tech company, IP diligence must cover:

**Ownership:**
- [ ] All employees and contractors have properly executed IP assignments
- [ ] No IP in limbo from university research or prior employer "moonlighting"
- [ ] IP schedule in purchase agreement is accurate and complete
- [ ] All IP is in the selling entity's name (not a founder's personal name)
- [ ] No undisclosed encumbrances, pledges, or security interests on IP

**Patent portfolio quality:**
- [ ] Review prosecution history for any claim scope narrowing (prosecution history estoppel)
- [ ] Confirm all maintenance fees are current
- [ ] Check for any reexamination or IPR proceedings
- [ ] Review all third-party licenses for assignment restrictions (some licenses are non-assignable without consent)

**Open source:**
- [ ] SBOM review — look for AGPL, GPL, SSPL, Commons Clause, or Business Source licenses in proprietary product code
- [ ] Review compliance with all open source license terms
- [ ] Confirm attribution requirements are met

**Litigation and claims:**
- [ ] Any pending or threatened IP infringement claims (patent, copyright, trademark, trade secret)
- [ ] Any prior settlements or licenses that limit future IP use
- [ ] Any standard essential patent commitments or FRAND obligations

**AI-specific:**
- [ ] Training data provenance and licensing
- [ ] Any outstanding copyright demands from content creators
- [ ] Model weights — do they contain third-party IP?

---

## AI-Specific IP Issues

### Who Owns AI Model Outputs?

**US law (current):** AI-generated outputs without sufficient human creative input are not copyrightable. The Supreme Court declined review of *Thaler v. Perlmutter* in March 2026, confirming this position.

**Practical impact:**
- Code generated by AI tools (Copilot, Cursor, etc.) may lack copyright protection for the generated portions
- For commercial software: human review, editing, and arrangement of AI-generated code creates a copyright interest in the human's contributions
- For AI-generated marketing content, designs, etc.: document human creative decisions to support copyright registration
- Competitors can freely copy pure AI-generated outputs — this is an underappreciated business risk

### AI Model Weights and Trade Secrets

Model weights are currently the primary IP asset for most AI companies, protected as trade secrets:

- Model weights can be registered as a trade secret under DTSA/state trade secret laws
- The economic value of trained model weights clearly qualifies — a trained model can represent billions in compute costs
- Protect via: (a) access controls on weight files, (b) encrypted storage, (c) restricted API access (no capability to extract weights), (d) contractual prohibitions in API terms of service against model extraction/distillation
- Model distillation (training a smaller model on the outputs of a larger model) may constitute trade secret misappropriation if the purpose is to replicate the model's behavior — document your API ToS prohibitions explicitly

### AI Patents Strategy

**What to patent in AI:**
- Novel training methods (specific algorithmic innovations, not just "we trained a neural network on X data")
- Novel architectures with specific technical improvements (measurable gains in efficiency, accuracy, or speed tied to a specific architectural element)
- Novel inference optimization techniques
- Novel hardware-software co-designs
- Novel data processing pipelines with technical novelty
- Applications of AI to solve specific technical problems in a non-obvious way

**What NOT to patent:**
- Bare software implementations of known algorithms
- "Using AI/ML to predict X" without a specific technical improvement to the method
- Business methods with only generic AI implementation

**AI patent drafting tips:**
- Lead with the technical problem being solved
- Quantify the technical improvement (speed, accuracy, efficiency benchmarks)
- Claim the specific architectural or algorithmic elements that produce the improvement
- Do not claim the training data or the trained model itself (those are trade secrets / potentially not patent-eligible)
- Claim both the method (training process) and the system (the trained model as a component of a larger technical system)

### Training Data Rights Strategy

**Data sourcing hierarchy (lowest to highest risk):**
1. Synthetically generated data (by your own models) — no third-party IP issues
2. Public domain data — free and clear
3. Licensed proprietary datasets — document license scope carefully; ensure commercial training use is explicitly permitted
4. Web-scraped data with copyright filtering — implement systematic copyright filtering; honor robots.txt and emerging opt-out mechanisms
5. Human-annotated data — own the annotations via contractor IP assignment; ensure contractors don't annotate copyrighted content without authorization
6. Licensed content via rights-holder partnerships — gold standard for content-sensitive domains

**EU AI Act compliance for training data (for GPAI model providers):**
- Article 53 of the EU AI Act requires GPAI providers to maintain technical documentation including a "sufficiently detailed summary about the content used for training" the model
- Must implement a policy to comply with EU copyright law, including the Article 4(3) DSM Directive opt-out mechanism
- Must make the copyright compliance policy publicly available
- First compliance deadline: August 2, 2026 for models already deployed; ongoing for new deployments

---

## Licensing Models

### Key Licensing Structures

**Royalty-bearing licenses:**
- **Lump sum**: Single payment for a license; appropriate for well-defined limited use
- **Running royalty**: Percentage of net sales or revenue; aligns licensor and licensee interests; requires audit rights
- **Milestone payments**: Triggered by specific events (regulatory approval, revenue threshold, etc.)
- **Hybrid**: Upfront fee + running royalties; common in tech licensing

**For patent licenses:**
- Define the licensed claims specifically (not just "all patents")
- Grant-back provisions: does the licensee grant back improvements? (Should be non-exclusive from licensee's perspective)
- Most Favored Licensee (MFL) clauses: licensor agrees not to grant more favorable terms to others — avoid these as a licensor if you want pricing flexibility
- Field-of-use restrictions: limit the license to specific applications
- Geographic restrictions: territory-specific licenses

**Cross-licensing:**
Cross-licensing involves two parties granting each other licenses to their respective patent portfolios. Common between companies with overlapping patents in the same technical field. Allows both parties to operate without infringement risk. Often arranged without monetary exchange ("royalty-free cross-license"). Can raise antitrust concerns if used to exclude third parties.

**Patent pools:**
A consortium of patent holders that aggregate patents for joint licensing to third parties on FRAND terms. Examples: MPEG-LA (video codecs), Via Licensing (audio), Avanci (automotive connectivity). Most relevant when you have Standard Essential Patents (SEPs) committed to a standards body. FRAND = Fair, Reasonable, and Non-Discriminatory — the licensor cannot refuse to license on terms that are commercially reasonable.

### Standard Essential Patents (SEPs) and FRAND

If your technology is incorporated into an industry standard (e.g., 5G, WiFi, JPEG), your patents may be Standard Essential Patents:
- You must declare them as essential to the relevant standards body (ETSI, IEEE, ISO, etc.)
- You must commit to license on FRAND terms
- Failure to license on FRAND terms can expose you to anti-trust liability
- FRAND rates are heavily litigated globally — the US, EU, China, and UK have different approaches
- For AI companies: most current AI standards (e.g., ONNX, MLPerf) do not create SEP obligations, but this will evolve

---

## IP Litigation Basics

### Patent Infringement Claims

**Anatomy of a patent infringement claim:**
1. Plaintiff holds a valid, enforceable patent
2. Defendant's product or method falls within the scope of at least one claim (literal or doctrine of equivalents)
3. Defendant had knowledge of the patent (relevant for enhanced damages / willfulness)

**Upon receiving a patent demand letter:**
1. **Do not panic and do not ignore it** — you have ~3 weeks to respond meaningfully
2. Consult patent litigation counsel immediately
3. Do NOT respond yourself without counsel
4. Begin document preservation (litigation hold) immediately
5. Assess: Is the claimant a practicing entity (building a product) or an NPE/patent troll?
6. Obtain a copy of the patent and the claims
7. Have counsel conduct a preliminary invalidity and non-infringement analysis
8. Consider: license, design around, challenge validity (IPR), or litigate

**Inter Partes Review (IPR):**
IPR is a USPTO administrative proceeding to challenge the validity of a granted patent. It is much faster and cheaper than district court litigation:
- Cost: $50,000–$300,000 vs. $3M+ for district court trial
- Timeline: Final decision typically within 18 months of institution
- Success rate: approximately 60–70% of challenged claims are cancelled or amended
- Key limitation (2026 proposed rules): the USPTO has proposed requiring IPR petitioners to stipulate they won't pursue invalidity challenges in other venues, which may reduce IPR's attractiveness as a defense tool
- IPR must be filed within 1 year of being served with a complaint alleging infringement

**Patent trolls (NPEs) — defense strategy:**
- NPEs hold patents but do not make products; they exist to license patents, often through litigation threat
- NPE-filed cases increased 15–20% in 2025 vs. 2024
- Defense options:
  - **LOT Network**: Joining LOT Network means that if another LOT member acquires a patent and asserts it against you, your LOT license automatically protects you — free for startups under $25M revenue
  - **Unified Patents**: Membership-based organization that files IPR petitions to invalidate weak patents commonly asserted against members (~$10,000–$50,000/year)
  - **Early settlement**: NPEs often accept $50,000–$200,000 settlements to avoid litigation; weigh against cost of defense
  - **IPR petition**: File an IPR to challenge patent validity, which can lead to cancellation or force settlement

### Cease and Desist Letters

**Sending a C&D:**
For trademark or copyright infringement (common scenarios):
1. Identify the infringement clearly (screenshots, documentation)
2. Have counsel draft the letter — a poorly drafted C&D can be used against you
3. C&D letter should: identify your rights (registration number, registration date), describe the infringing conduct, demand specific action (cease use, destroy inventory, provide accounting), set a deadline (typically 10–21 days)
4. Send via certified mail and email to legal contact if known
5. Retain copies of all communications for potential litigation

**Receiving a C&D:**
1. Consult counsel immediately — do not respond without review
2. The C&D is not a lawsuit — you have time to assess
3. Options: (a) comply if infringement is clear, (b) negotiate, (c) challenge the validity of their IP rights, (d) ignore (risky if rights are valid)
4. Do not destroy records in anticipation of litigation

### Trademark Infringement and Monitoring

- Set up Google Alerts and USPTO TESS monitoring for confusingly similar marks
- Subscribe to trademark watch services (e.g., CompuMark, Dennemeyer) for ~$500–$2,000/year — essential once your brand has value
- The TTAB (Trademark Trial and Appeal Board) handles oppositions and cancellations — less expensive than federal court but still requires counsel ($5,000–$30,000+ for TTAB proceedings)

---

## International IP Strategy

### China IP Strategy

China is the world's most active patent filing jurisdiction and a critical market for most tech companies. China is a first-to-file country — if you have any China market ambitions, file in China early.

**China patent strategy:**
- File via PCT with China designation, or directly with CNIPA
- CNIPA examination time: now averaging ~15.7 months (competitive with USPTO)
- China also has Utility Model patents (20-year term, registered without examination, faster — good for tactical blocking) and Design patents (15-year term)
- Patent validity disputes in China have become increasingly serious — the Chinese court system now provides meaningful enforcement
- AI patent pool: China's CNIPA is establishing a patent pool for AI large language models — monitor this development

**China trademark strategy:**
- File in China independently — do not rely solely on Madrid Protocol
- China is first-to-file for trademarks; bad-faith pre-registration by "trademark squatters" is endemic
- File in all relevant classes, including defensive filings in adjacent classes
- File in Chinese characters as well as your Latin-alphabet mark (separate applications)
- Chinese trademark registration is relatively inexpensive (~$300–$800 per class) — file early

**China trade secrets:**
- China's Unfair Competition Law and Trade Secret Law provide trade secret protections, improved significantly in recent years
- But enforcement remains challenging and fact-intensive
- For tech transferred to China JV partners or licensees: structure agreements carefully; use technical segmentation (give partners only what they need); retain critical know-how internally

### EU AI Act and IP (Summary for Deployment)

For AI companies deploying in the EU, the AI Act imposes IP-relevant obligations:

| Obligation | Who | Deadline | Key Requirement |
|------------|-----|----------|----------------|
| Copyright compliance policy | GPAI model providers | August 2, 2026 | Publicly available policy; implement opt-out mechanisms |
| Training data summary | GPAI model providers | August 2, 2026 | Technical documentation with "sufficiently detailed summary" of training content |
| Transparency to downstream deployers | GPAI model providers | August 2, 2026 | Provide information about copyrighted training data |
| Systemic risk assessment | GPAI with "systemic risk" designation (>10^25 FLOPS training compute) | Ongoing | Adversarial testing, incident reporting |

The EU currently has no specific rules on the copyrightability of AI-generated works; the European Parliament has called for new legislation but nothing has passed as of June 2026.

---

## Common IP Mistakes Startups Make

### Fatal Mistakes (Can Kill a Deal or the Company)

1. **Founders not signing IP assignment before equity issuance**: If founders wrote code before incorporation and never formally assigned it to the company, the company may not legally own its core product. This surfaces in every Series A due diligence and can delay or kill deals.

2. **Contractors who own your code**: Hiring a development agency or freelancer without a written assignment clause. "Work made for hire" does not transfer patent rights and may not transfer copyright for contractors. Years later, when you're raising a $50M round, the contractor may assert ownership.

3. **Public disclosure before patent filing**: Presenting at a conference, publishing a blog post, or demo-ing at a trade show before filing even a provisional patent application. In most international markets, this permanently bars patent protection. In the US you have one year — but many founders waste this grace period.

4. **AGPL dependency buried in your proprietary product**: One AGPL library can require open-sourcing your entire commercial product. Synopsys found 53% of audited codebases had license conflicts. Do not wait until M&A diligence to discover this.

5. **No trade secret program**: Conducting trade secret litigation without evidence that you treated the information as a secret is extremely difficult. If you want trade secret protection, you must implement and document protective measures from the start.

6. **Trademark infringement at launch**: Launching a product with a name that is confusingly similar to an existing registered trademark invites a C&D letter and potentially a lawsuit requiring expensive rebrand. A clearance search costs $500–$2,000 — cheap insurance.

7. **University IP taint**: If founders developed core technology at a university using university resources, the university may own the IP or have rights to it. Address this explicitly — either confirm the IP was developed entirely outside the university's scope, or obtain a proper license or assignment from the university's tech transfer office.

### Costly Mistakes (Expensive to Fix)

8. **Waiting too long to file patents**: Features ship, products launch, competitors appear — and no provisional was ever filed. The one-year grace period from US public disclosure is gone. File provisionals early, often, and for anything genuinely novel.

9. **Broad, unenforceable patent claims**: Filing patents with claims so broad they are clearly invalid (prior art exists), or so narrow they are trivially designed around. Work with a skilled patent prosecutor who understands both patent law and your technology.

10. **No international trademark filing**: Building a $50M brand on a US-only trademark, then discovering the mark is owned by someone else in Europe or China when you try to expand. The Madrid Protocol filing costs $3,000–$7,000 for major jurisdictions — do it at Series A.

11. **Non-competes in California**: Drafting a non-compete for California employees and relying on it. California will void non-competes, even with choice-of-law clauses selecting other states. Focus on trade secret protection instead.

12. **Inadequate provisional patents**: Filing a one-page provisional that doesn't adequately describe the invention, then trying to claim priority to it for the non-provisional. An inadequate provisional provides no protection. A provisional must include enough detail to support the non-provisional claims.

13. **Missing standard open source attribution**: MIT, BSD, and Apache 2.0 licenses require attribution. Shipping a product without the required notices and copyright statements, while low-risk in litigation terms, creates documentation issues in M&A diligence.

14. **Assuming AI tools mean you own the output without copyright**: Believing that because you "created" it with an AI tool, you hold copyright. AI-generated outputs (without sufficient human creative input) are not copyrightable in the US. This has practical implications for competitive defensibility of AI-generated content.

### Strategic Errors

15. **Patenting too narrowly, too broadly, or the wrong things**: Filing patents on features rather than the underlying technical innovations; filing claims that read only on your exact implementation (trivially designed around); or spending patent budget on non-core technology.

16. **Treating IP as a compliance checkbox**: IP strategy divorced from business strategy is wasted money. Patent what your competitors cannot design around; trade secret what cannot be reverse-engineered or patented; trademark what creates brand value in your market.

17. **No IP strategy at all**: "We're a startup, we don't need patents." This may be true at day 1, but a $20M funded company with no IP strategy is leaving significant M&A and licensing value on the table — and is undefended against competitor copying.

---

## Quick Reference: Action Items by Situation

| Situation | Immediate Actions |
|-----------|-------------------|
| About to make first hire | PIIA template ready; have attorney review for target state |
| About to hire first contractor | IP assignment + NDA signed before work begins |
| Pre-product launch | Trademark clearance search; file provisional patent applications; open source license scan |
| New investor asking for data room | Compile patent schedule, PIIA confirmations, SBOM, trademark registrations |
| Received patent demand letter | Preserve documents; engage patent litigation counsel within 72 hours |
| Received DMCA takedown | Review for compliance; respond within statutory window if counternotice warranted |
| About to launch in EU | EU trademark filing; review GDPR data practices for training data; AI Act compliance assessment |
| About to launch in China | File China trademark immediately; file PCT with China designation; review any China JV IP terms |
| M&A process beginning (as target) | Conduct IP audit; address any open source issues; confirm all assignments are in order |
| Discovered AGPL dependency | Engage counsel; assess contamination scope; prepare remediation plan before disclosure |
| Key engineer departing to competitor | Send departure reminder of obligations; revoke access immediately; consult counsel |

---

## Disclaimer

The information in this skill is for general educational and informational purposes only. It does not constitute legal advice, and no attorney-client relationship is formed by any interaction with this skill. IP law is highly jurisdiction-specific and fact-dependent. Laws change rapidly, particularly in AI-related IP. Always consult a licensed attorney in the relevant jurisdiction for advice on your specific situation before taking any legal action or making significant IP decisions. This skill is current as of mid-2026 based on available information; specific decisions, regulations, and case law may have changed.
