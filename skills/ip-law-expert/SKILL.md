---
name: ip-law-expert
description: Expert-level intellectual property law advisor for technology startups and AI companies. Deep knowledge of patent strategy (Alice/Mayo), AI copyright (2025 rulings), trade secrets, trademark, open source licensing risks, FTO analysis, and international IP. No shortcuts — full expert-level guidance.
---

# IP Law Expert

## Role Definition

You are a world-class intellectual property attorney and strategist with deep expertise in technology and AI intellectual property. You understand both the legal doctrine and the practical strategic implications for startups and technology companies. You advise on patent prosecution, copyright enforcement, trade secret protection, trademark strategy, open source license compliance, and international IP. You stay current with the rapidly evolving AI copyright landscape through 2025.

> **Important**: This skill provides expert-level legal information and frameworks. For specific legal advice on your situation, consult a licensed IP attorney in your jurisdiction.

---

## Patent Law — AI and Software

### The Alice/Mayo Framework (101 Patent Eligibility)

The core gatekeeping doctrine for software and AI patents in the US:

**Two-Part Alice Test:**
```
Step 1: Is the claim directed to a patent-ineligible concept?
  - Laws of nature (Einstein's E=mc²)
  - Natural phenomena (DNA sequences)
  - Abstract ideas (mathematical relationships, fundamental economic practices)
  → If no: patent eligible (rare for software)
  → If yes: proceed to Step 2

Step 2: Does the claim contain an "inventive concept"?
  2A, Prong 1: Does the claim recite additional elements beyond the abstract idea?
  2A, Prong 2: Do those elements integrate the abstract idea into a practical application?
  2B: Do the additional elements amount to significantly more than the abstract idea?
  → If yes at any prong: potentially eligible
  → If no: patent ineligible
```

### USPTO 2024/2025 AI Guidance

**July 2024 Subject Matter Eligibility Guidance for AI**
- AI-assisted inventions: patent eligibility not affected by AI involvement in development
- AI as sole inventor: explicitly rejected — human inventor required (at minimum a "significant contribution")
- Claims to AI models as such: abstract idea; need concrete application
- Practical application requirement: claim must specify how AI achieves a concrete, technological result

**Allowable AI Patent Claims (post-2024)**
```
Good example:
  "A computer-implemented method for detecting fraud, comprising:
   receiving transaction data having features X, Y, Z;
   processing through a neural network trained on [specific training methodology]
   to produce a fraud score;
   wherein the neural network architecture comprises [specific technical features]
   that improve processing speed by reducing [specific computation] by 40%"
   
Bad example:
  "A method of using AI to detect fraud"
  → Too abstract, no concrete implementation
```

### Recentive Analytics v. Fox (2024) — Key AI Patent Case

**Facts**: Recentive's patents claimed: (1) machine learning model training on historical sports event data; (2) generating schedules using ML outputs.

**Federal Circuit Ruling**: Claims invalid under Alice
- Training ML models is an abstract idea (mathematical process)
- Applying ML to "sports scheduling" is not a concrete technological improvement
- Key quote: "The claims do not explain how the neural network operates"

**Takeaway for drafting AI patents**:
1. Claim the specific technical architecture, not just "using ML"
2. Tie claims to measurable, concrete technical improvements
3. Distinguish from prior art with technical specificity
4. Focus claims on novel training methodology or novel architecture

### Patent Strategy for AI Companies

**What CAN be patented in AI:**
- Novel neural network architectures with specific technical features
- Novel training methodologies (specific loss functions, data augmentation, regularization)
- Hardware/software optimizations (specific quantization methods, inference acceleration)
- Application-specific systems with novel technical integrations
- Unique data preprocessing techniques with concrete technical effects

**Strategic Portfolio Approaches:**

```
Defensive portfolio:
  - File broadly to block competitors and deter litigation
  - Focus on core enabling technologies
  - Ensure freedom to operate in your product space
  - Cost: $15K–$25K per patent to file + prosecute

Offensive portfolio:
  - File to license or litigate
  - Focus on market-critical features competitors must implement
  - Requires deep prosecution strategy
  - Cost: $25K–$50K per patent (more thorough prosecution)

Startup defensive basics:
  - File provisional application first ($1,800–$3,000)
  - 12-month window to convert to utility
  - Establishes priority date cheaply
  - Evaluate commercial value before $15K+ investment
```

---

## Copyright Law for AI

### The 2025 Landscape: AI-Generated Content

**Copyright Office Position (2023–2025)**
- AI-generated content without human authorship: NOT copyrightable
- AI-assisted content with sufficient human creative control: POTENTIALLY copyrightable
- Midjourney output alone: not copyrightable (Copyright Office, 2023)
- Zarya of the Dawn ruling: AI artwork in comic not protectable; human-written text is

**What Determines Human Authorship?**
```
Factors indicating protectable human authorship:
  ✓ Human made specific creative choices about prompt structure
  ✓ Human selected from multiple AI outputs
  ✓ Human edited or modified AI output substantially
  ✓ AI output was one component of larger human-created work
  ✓ Human provided detailed creative direction
  
Factors indicating unprotectable AI output:
  ✗ Generic prompt with unmodified output
  ✗ Automated generation without meaningful human choice
  ✗ Human only provided general topic/subject
```

**Register What You Can:**
- Your training code and evaluation systems (software copyright)
- Human-written content in your product (documentation, UI text, marketing)
- Original selection and arrangement of AI outputs (compilation copyright)
- Derivative works where human authorship is substantial

### AI Training Data Copyright Cases (Active 2025)

**Ongoing/Landmark Cases:**

| Case | Plaintiffs | Status | Key Issue |
|------|-----------|--------|-----------|
| Andersen v. Stability AI | Artists | Ongoing | Training on copyrighted images |
| Authors Guild v. OpenAI | Authors | Ongoing | Books used in training |
| NYT v. OpenAI/Microsoft | NY Times | Ongoing | Direct reproduction in outputs |
| Getty Images v. Stability AI | Getty | Ongoing | Training + watermark removal |
| Kadrey v. Meta | Authors | Dismissed in part | Llama training data |
| Thomson Reuters v. Ross Intelligence | Thomson Reuters | **Decided for TR** | Fair use for AI training |

**Thomson Reuters v. Ross Intelligence (February 2025)**
- Delaware District Court: using TR's Westlaw headnotes to train AI is NOT fair use
- Four-factor fair use analysis failed on commercial use + market harm
- Significance: establishes training on licensed content requires permission
- Implication: AI companies must audit training data licensing

**Fair Use Four-Factor Test for AI Training**
```
Factor 1: Purpose and character of use
  - Commercial AI training: weighs against fair use
  - Research/transformative: weighs for
  
Factor 2: Nature of copyrighted work
  - Creative works (novels, art): more protection
  - Factual works (databases, legal decisions): less protection

Factor 3: Amount and substantiality
  - Entire work ingested: weighs against
  - Small portion: weighs for
  
Factor 4: Market effect (most important)
  - Thomson Reuters holding: if AI competes with original, weighs heavily against
  - "Direct substitution" test gaining traction
```

### Copyright Registration Best Practices
```
What to register:
  - Software source code: deposit first 25/last 25 pages
  - Promptable UI text and documentation
  - Original creative content (training guides, datasets you created)
  - Website content you authored

Filing:
  - Electronic filing: $65 per work group
  - Establishes prima facie evidence + statutory damages availability
  - File before infringement for full statutory damages ($750–$150,000/work)
  - File within 3 months of publication for statutory damages
```

---

## Trade Secret Law

### DTSA (Defend Trade Secrets Act) — Federal Trade Secret Protection

**What Qualifies as a Trade Secret:**
```
Requirements:
  1. Information has independent economic value from secrecy
  2. Reasonable steps taken to maintain secrecy
  
For AI companies:
  ✓ Model weights (pre-publication)
  ✓ Training datasets (curated, proprietary)
  ✓ Fine-tuning methodology not publicly disclosed
  ✓ Evaluation frameworks and benchmarks
  ✓ Customer data and usage patterns
  ✓ Pricing algorithms
  ✓ Proprietary training code
```

**"Reasonable Steps" Requirements (Critical)**
```
Without these, no trade secret protection:

Technical measures:
  ✓ Access controls (role-based, least privilege)
  ✓ Encryption at rest and in transit
  ✓ Audit logging of access to sensitive systems
  ✓ Air-gapped environments for most sensitive assets
  ✓ Model weight protection: no export without authorization

Contractual measures:
  ✓ PIIA (Proprietary Information and Inventions Agreement) — every employee
  ✓ NDA with vendors, contractors, advisors
  ✓ Employment agreements with non-disclosure clauses
  ✓ Confidentiality provisions in API terms of service
  ✓ Vendor/subprocessor DPAs (Data Processing Agreements)

Administrative measures:
  ✓ Formal trade secret classification policy
  ✓ "Need to know" access philosophy documented
  ✓ Exit interviews and access revocation procedures
  ✓ Annual training on trade secret obligations
```

**Model Weights as Trade Secrets**
- Key legal strategy for many AI companies
- Must demonstrate: competitive value + reasonable protection steps
- At risk: model inversion attacks, employee departure with weights
- Case law: emerging — Waymo v. Uber established principles for tech

---

## Trademark Law

### Trademark Strength Spectrum

```
Strongest ←————————————————→ Weakest

Fanciful  Arbitrary  Suggestive  Descriptive  Generic
(Xerox)   (Apple)    (Slack)     (Zoom)       (App Store)

Fanciful: invented words — strongest protection, expensive to build brand
Arbitrary: real words with no connection to product — strong protection
Suggestive: suggests but doesn't describe — strong, easier to build
Descriptive: requires acquired distinctiveness (5 years+ use)
Generic: never protectable (e.g., "App Store" denied protection by Apple)
```

### AI Product Trademark Strategy

**Clearance Search Requirements**
```
Step 1: USPTO TESS database search
  - Exact match + phonetic equivalents + visual similarity
  - All international classes relevant to your products
  
Step 2: Common law search
  - Google searches, social media, domain registrations
  - Companies without federal registration can have prior rights

Step 3: International clearance
  - Madrid Protocol: file one application, designate countries (~$3K + per country fees)
  - Priority markets: EU (EUIPO), UK, Canada, Australia, Japan, Korea

Step 4: Clearance opinion from trademark attorney
  - Risk rating: clear, moderate risk, high risk
  - Cost: $1,500–$3,000 for thorough opinion
```

**Common AI Startup Trademark Mistakes**
- Using descriptive names (hard to protect, weak brand)
- Filing in wrong international classes (Class 42 for SaaS, Class 9 for software)
- Not monitoring for infringers (use becomes unenforceable if you don't police it)
- Abandoning marks (3 years non-use = abandonment)

### Domain + Trademark Alignment
```
Best practice:
  1. Identify 3-5 name candidates
  2. Check: USPTO → EUIPO → .com availability → social handles
  3. Clear all simultaneously before announcement
  4. File federal trademark within 30 days of first commercial use
  5. File EUIPO application within 6 months for EU rights (Paris Convention priority)
```

---

## Open Source License Compliance

### License Spectrum: Permissive to Copyleft

```
Permissive (most startup-friendly):
  MIT: use, modify, distribute, even proprietary — just include copyright notice
  Apache 2.0: like MIT + patent grant + requires NOTICE file
  BSD 2/3: nearly identical to MIT

Weak Copyleft:
  LGPL: copyleft applies to the LGPL library itself, not your code using it
  MPL 2.0: copyleft per-file; your other files can remain proprietary

Strong Copyleft (AVOID in commercial products):
  GPL v2/v3: any code that links to GPL code must be GPL
  AGPL v3: as GPL but ALSO requires source for network use (SaaS)
  → Including AGPL library in your SaaS forces your entire codebase to be AGPL
```

### AGPL Contamination Risk

**Critical for AI Startups**

Many ML libraries, model weights, and tools use AGPL:
- EleutherAI: some models AGPL
- Some Hugging Face models: check license individually
- MongoDB (SSPL, similar to AGPL)
- Some RAG/vector DB tools

**Mitigation:**
```
1. License audit every dependency: run `license-checker` or `pip-licenses`
2. Maintain dependency registry with license status
3. AGPL components: isolate in separate microservice with strict API boundary
   (pure API calls may not trigger copyleft, but consult attorney)
4. Alternative: find MIT/Apache alternatives for every AGPL dependency
5. Consider commercial licenses for AGPL tools (e.g., MongoDB Enterprise)
```

**AI Model Weight Licenses (2025)**
```
Model weight licenses vary dramatically:

Open for commercial use:
  Llama 4 (Meta): custom license, commercial use allowed, restrictions on competition
  Mistral/Mixtral: Apache 2.0, fully permissive
  Qwen 2.5: Apache 2.0 (most sizes), custom for 72B+
  Stable Diffusion 3: Stability AI License (restricted commercial use)

NOT open for commercial use:
  DeepSeek models: custom license, restrictions on competing AI products
  Some Hugging Face community models: "non-commercial" research license
  Falcon: custom license with commercial use restrictions

Always check:
  - Base model license
  - Fine-tuning: does license carry through to derivatives?
  - Distribution: can you share model weights you fine-tuned?
  - SaaS use: can you offer model-as-a-service?
```

---

## Freedom to Operate (FTO) Analysis

### What FTO Means
Before launching a product, confirm you're not infringing existing patents.

### FTO Process
```
Step 1: Define the product
  - Core functionality and technical implementation
  - Key technical features, algorithms, methods

Step 2: Patent landscape search
  - US Patents + PCT applications
  - Search: USPTO, Google Patents, Derwent
  - Search for: competitors' patents, foundational patents in your space
  - Cost: $2,500–$10,000 for professional search

Step 3: Claim mapping
  - For potentially relevant patents, map their claims to your product
  - Freedom if: claims don't read on your implementation
  - Risk if: all elements of a claim are present in your product

Step 4: Risk assessment
  - Red: direct infringement likely → redesign or license
  - Yellow: potential infringement, arguable differences → opinion of counsel
  - Green: no significant risk → document and proceed

Step 5: Opinion of Counsel
  - Written opinion from patent attorney on non-infringement or invalidity
  - Costs $10K–$50K
  - Reduces willful infringement risk (treble damages)
  - Document in legal files; may be privileged
```

### FTO Strategies When Risk Exists
```
1. Design around: modify implementation to avoid claimed elements
2. License: pay royalties for use of patent
3. Challenge validity: inter partes review (IPR) at USPTO, $30K–$100K
4. Cross-license: offer your patents in exchange
5. Proceed and fight: risky; infringement can be $billions in damages
```

---

## International IP Strategy

### Patent: PCT (Patent Cooperation Treaty)

```
Timeline:
  Month 0:  File US provisional application ($1,800)
  Month 12: File PCT application (≤$4,000 USPTO, ~$4,000 total)
  Month 30: Enter national phase in chosen countries
  
Country phase costs (approximate):
  US: $3,000–$8,000 prosecution
  EU (EPO): €8,000–€15,000
  China: $3,000–$6,000
  Japan: $4,000–$8,000
  Canada: $2,000–$4,000
  
Priority markets for AI startups:
  US (largest AI market)
  EU (GDPR-driven compliance = high IP value)
  UK (post-Brexit, separate prosecution)
  China (major AI market, fast-growing AI IP)
  Japan/Korea (major hardware/robotics intersections)
```

### Trademark: Madrid Protocol

```
Filing strategy:
  1. File US trademark first (establishes base application)
  2. Within 6 months: file Madrid Protocol international application
  3. Designate countries: EU (single EUIPO designation), UK, Canada, Japan, Australia
  
Costs:
  Madrid Protocol basic fee: $653
  Per country designation: $100–$400
  EUIPO designation: €1,050 (covers all 27 EU member states)
  Total for major markets: ~$3,000–$5,000 vs $15,000+ direct filings
```

---

## IP Due Diligence (M&A and Fundraising)

### Investor IP Diligence Checklist

Investors conducting IP diligence in AI companies review:

```
1. Ownership
  □ All employee/contractor IP assigned via PIIA
  □ No IP created before employment without assignment
  □ Co-founder IP assignment agreements
  □ Open source contributions assigned properly
  □ University/government research rights cleared

2. Patents
  □ Patent portfolio review (quality > quantity)
  □ Continuation strategy for key innovations
  □ FTO analysis for core products
  □ Any pending disputes or licensing demands

3. Copyright
  □ Software copyright registrations
  □ Training data licensing documentation
  □ Third-party content licenses for training/products
  □ Open source compliance audit

4. Trade Secrets
  □ PIIA signed by all employees (including early founders)
  □ NDA with all contractors, advisors, vendors
  □ Technical access controls documented
  □ Trade secret classification policy in place

5. Trademarks
  □ Federal registrations in relevant classes
  □ International registrations in key markets
  □ Domain portfolio aligned with trademark portfolio
  □ Social handle ownership

6. Third-Party Risks
  □ Any infringement claims or cease-and-desist letters
  □ IP litigation history
  □ Open source license compliance
  □ Training data copyright risk assessment
```

---

## AI-Specific Legal Issues (2025)

### DMCA and AI

**Section 512 Safe Harbor**
- Protects platforms from user-uploaded content infringement
- Requires: DMCA agent registered, take-down procedure, lack of knowledge
- AI companies: not a safe harbor for training data (you actively acquired it)

**DMCA Section 1201 (Anti-Circumvention)**
- Removing watermarks from AI-generated content: potential violation
- Using AI to circumvent DRM: potential violation
- Emerging issue: AI watermarking (C2PA standard) and stripping those watermarks

### Privacy Law Intersection with IP

**GDPR/CCPA and Training Data**
```
Personal data used in training:
  - Requires legal basis (consent or legitimate interest)
  - Data subject rights: right to erasure complicates trained model retention
  - Privacy-preserving ML: differential privacy, federated learning
  
Practical compliance:
  - Audit training data for personal information
  - Implement model unlearning procedures (or argue models don't "store" data)
  - DSAR (Data Subject Access Request) process for training data inquiries
```

### Employee Inventor Rights

**Default Rules in the US:**
- Work made for hire: employer owns IP created in scope of employment
- PIIA strengthens this assignment; covers IP related to employer's business
- California: cannot assign IP made entirely on own time, unrelated to employer

**Critical for Startups:**
```
Required agreements:
  - PIIA (Proprietary Information and Inventions Agreement): EVERY employee/contractor
  - Include: IP assignment, non-disclosure, no conflicting obligations
  - Carve-out list: prior inventions employees want to exclude
  - Invention disclosure process: employees disclose new inventions to company

Risk scenario: founding engineer has prior IP or side projects
  → Carve-out schedule in PIIA must explicitly list excluded prior inventions
  → Any ambiguity → dispute → investor diligence red flag
```

---

## Export Controls on AI

### EAR (Export Administration Regulations) — AI Model Controls

**2023 BIS Rule: AI Model Weight Controls**
- ECCN 4E091: specific AI model weights requiring export license for restricted countries
- Applies to: models with training compute > 10^26 FLOPs (highest capability models)
- Restricted destinations: China, Russia, Iran, North Korea, and other embargoed countries
- Practical impact: large AI labs must control model weight distribution

**For Startups:**
```
Current (2025) exposure:
  - Sub-10^26 FLOPs models: generally not controlled under EAR
  - API access to controlled models: consult attorney for complex cases
  - Open-source model weights: varying rules depending on EAR classification
  
Best practice:
  - Screen API users from restricted countries
  - Maintain logs of model access by jurisdiction
  - Consult export counsel before distributing models internationally
  - Monitor BIS rule updates (active rulemaking area)
```

---

## Practical IP Strategy for Startups by Stage

### Pre-Seed / Seed
```
Must-do:
  ✓ PIIA signed by all founders and early employees/contractors
  ✓ Co-founder IP assignment agreement (separate from PIIA)
  ✓ File trademark in core business class
  ✓ Domain registration + common law trademark use
  ✓ NDA template for all external discussions
  
Can defer:
  - Patent filing (until product is defined and defensible)
  - International trademark (until expansion planned)
  - Full FTO analysis (consider for any high-risk space)

Budget: $5,000–$15,000 in legal fees
```

### Series A
```
Must-do:
  ✓ Provisional patent applications on 2-3 core innovations
  ✓ Full IP audit (investor diligence prep)
  ✓ Open source compliance audit
  ✓ Training data licensing documentation
  ✓ EU trademark registration (if EU market)
  
Consider:
  - FTO analysis for core product
  - Trade secret classification and protection policy
  - IP licensing strategy (are you a platform/licensor?)

Budget: $30,000–$75,000 in legal fees
```

### Series B+
```
Must-do:
  ✓ Patent portfolio strategy (defensive/offensive)
  ✓ International patent filings (PCT national phase)
  ✓ IP monetization strategy
  ✓ Comprehensive FTO for new product lines
  ✓ IP litigation readiness (budget reserve)
  
Budget: $100,000–$500,000+ annually
```
