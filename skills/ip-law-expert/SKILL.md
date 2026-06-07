---
name: ip-law-expert
description: Expert-level reference for startup founders and executives on intellectual property law in 2025–2026, covering patents, copyright, trade secrets, trademarks, software licensing, AI-generated IP, employee agreements, and international IP strategy. For informational/educational purposes only — not legal advice.
---

# IP Law Expert

*Note: This document is for informational and educational purposes only. It does not constitute legal advice. Consult a licensed attorney for specific legal matters.*

## 1. Patents: Fundamentals and Software Patent Strategy

### Patent Eligibility: The Alice/Mayo Framework

For software patents in the US, the **Alice Corp. v. CLS Bank International (2014)** ruling established the two-step test under 35 U.S.C. § 101:

**Step 1 (Alice Step 2A, Prong 1):** Is the claim directed to an abstract idea, mathematical concept, or mental process?

**Step 2 (Alice Step 2A, Prong 2):** If yes, does the claim include an "inventive concept" that amounts to significantly more than the abstract idea itself?

**What survives Alice (generally patentable):**
- Software claims that improve computer functionality itself (not just using a computer)
- Claims with specific technical solutions to technical problems
- Machine learning architectures with novel architectural innovations
- Hardware-software combinations with specific physical improvements

**What fails Alice (generally not patentable):**
- "Apply it on a computer" claims
- Business method claims with abstract steps
- Data processing claims without technical differentiation

**Drafting strategy post-Alice:**
```
WEAK: "A method for processing data comprising: receiving data; analyzing 
      the data to identify patterns; returning results"

STRONGER: "A method for reducing inference latency in a neural network 
          comprising: applying speculative decoding using a draft model 
          to predict n tokens in parallel; verifying predicted tokens 
          using the target model in a single forward pass; accepting 
          verified tokens to reduce p50 latency by [X]%"
```

### Patent Types Relevant to Software/AI Startups

| Patent Type | Term | Best For |
|---|---|---|
| **Utility Patent** | 20 years from filing | Algorithms, software architecture, methods |
| **Provisional Patent** | 12 months (converts to utility) | Filing date priority, MVP protection |
| **Design Patent** | 15 years | UI/UX designs, visual trade dress |
| **Continuation** | Same term as parent | Broader claims on already-disclosed inventions |

### USPTO Filing Process

```
1. Invention Disclosure Document (internal) → 2 weeks
2. Patentability search (prior art) → 2-4 weeks
3. Provisional Application (if time-sensitive) → immediate priority date
4. Utility Application filing → 3-6 months (with claims drafted)
5. Publication (18 months after filing date)
6. Examination (12-18 months after filing)
7. Final rejection / Notice of Allowance
8. Issue and maintenance fees (3.5, 7.5, 11.5 years)
```

**Cost ranges (with patent counsel, 2025):**
- Provisional: $2,000–$5,000
- Utility (complex software): $15,000–$25,000 to file
- Total through issuance: $20,000–$50,000 per patent

### AI-Specific Patent Strategy

**Patentable aspects of AI systems:**
1. Novel training methodologies (not just "use gradient descent")
2. Specific architectural innovations (attention mechanisms, novel layer types)
3. Data pipeline inventions (novel preprocessing, augmentation)
4. Inference optimization techniques (quantization, serving architecture)
5. Human-AI interaction systems (novel UX + AI integration)

**Non-patentable AI elements:**
- Training data itself (copyright/trade secret, not patent)
- Model weights (copyright/trade secret, not patent)
- Generic "apply ML to problem X" claims

---

## 2. Copyright in AI-Related Work

### Copyright in Software

Copyright attaches automatically upon creation of original expression. For software startups:

**What copyright protects:**
- Source code (both literal and non-literal elements)
- Training datasets (if original selection/arrangement)
- Documentation
- User interface elements (to some extent)

**What copyright does NOT protect:**
- Ideas, algorithms, methods (these need patents)
- Functional elements (merger doctrine: if there's only one way to express an idea, copyright doesn't cover it)
- Facts and unoriginal data

**Work for hire in software:**
```
EMPLOYEES: Work created within scope of employment = company owns it automatically (WFH)
CONTRACTORS: Must have written agreement assigning IP to company
             WITHOUT assignment: contractor owns the code (dangerous!)
```

### AI-Generated Content and Copyright (2024–2026)

**US position (Copyright Office guidance, 2023–2025):**
- AI-generated content with no human authorship is NOT copyrightable
- Works with substantial human creative expression that used AI as a tool MAY be protected
- The human must "select and arrange" elements with sufficient originality
- Registration is possible for the human-authored portions

**Key cases:**
- **Thaler v. Perlmutter (D.D.C. 2023)**: AI cannot be an author under Copyright Act
- **Andersen v. Stability AI (N.D. Cal.)**: Training on copyrighted images — ongoing
- **Getty Images v. Stability AI**: Training data copyright infringement claim — ongoing

**Practical guidance for AI startups:**
1. Document human creative decisions in AI-assisted work
2. Do not claim copyright in purely AI-generated outputs
3. Use open/licensed training data (CC0, CC-BY, or licensed datasets)
4. Obtain indemnification from AI model providers for training data claims

### Open Source Compliance

**Key license categories:**
| License | Copyleft Type | Commercial Use | Distribution |
|---|---|---|---|
| **MIT** | None | Yes | Preserve attribution |
| **Apache 2.0** | None | Yes | Preserve attribution + patent grant |
| **BSD 2/3** | None | Yes | Preserve attribution |
| **GPL 2.0** | Strong copyleft | Yes | Must open-source derivative works |
| **GPL 3.0** | Strong copyleft | Yes | Like GPL 2 + anti-tivoization |
| **LGPL** | Weak copyleft | Yes | Library use OK without opening app |
| **AGPL 3.0** | Network copyleft | Yes | Server-side use triggers copyleft |
| **CC0** | None | Yes | Public domain dedication |

**Open source risk matrix:**
```
SAFE for commercial products (no copyleft):
  MIT, Apache 2.0, BSD, ISC, CC0

RISKY (strong copyleft):
  GPL 2.0, GPL 3.0 — can "infect" proprietary code if linked
  AGPL 3.0 — server-side use counts as distribution

SAFE if used as a library (weak copyleft):
  LGPL — dynamic linking OK, must share LGPL modifications

UNKNOWN/UNSAFE:
  "Non-commercial only" — not OSI-approved, commercial use restricted
  SSPL (MongoDB) — controversial, may require open-sourcing all infra
```

**Compliance process:**
```python
# Use FOSSA, Snyk, or Black Duck to automate license scanning
# Run on every dependency update (CI/CD integration)

# Manual checklist for new dependencies:
1. What license does this package use?
2. Does it change between versions?
3. If copyleft: how is it linked? (dynamic vs. static)
4. Is it on your approved list?
5. Does your enterprise agreement require attribution?
```

---

## 3. Trade Secrets

### Definition and Protection Requirements (DTSA)

The **Defend Trade Secrets Act (DTSA, 2016)** provides a federal cause of action for trade secret misappropriation. State law (Uniform Trade Secrets Act in most states) also applies.

**A trade secret must:**
1. Derive independent economic value from not being publicly known
2. Be the subject of reasonable efforts to maintain its secrecy

**What qualifies as a trade secret in AI:**
- Model weights and architecture specifics
- Training datasets (proprietary composition)
- Fine-tuning methodologies and results
- System prompts and prompt engineering techniques
- Customer data and usage patterns
- Pricing algorithms and financial models

**Reasonable measures (what courts look for):**
```
Technical controls:
  ✓ Encryption at rest and in transit
  ✓ Access controls / least privilege
  ✓ Audit logging of access to trade secrets
  ✓ Separate systems for sensitive code/data

Legal controls:
  ✓ NDA with all employees (day 1)
  ✓ NDA with contractors before any IP sharing
  ✓ NDA with vendors, investors, potential partners
  ✓ PIIA (Proprietary Information and Inventions Agreement)

Operational controls:
  ✓ "Confidential" marking on documents
  ✓ Off-boarding checklist includes data return/deletion
  ✓ Limited sharing (need-to-know)
  ✓ Employee trade secret training
```

**DTSA remedies:**
- Injunctive relief to prevent disclosure/use
- Damages (actual + unjust enrichment) or reasonable royalties
- Exemplary damages up to 2x in cases of willful misappropriation
- Attorney's fees in willful cases
- **Ex parte seizure order** — emergency federal court order to seize misappropriated materials before the defendant can destroy them (rare but powerful)

---

## 4. Trademarks

### Trademark Fundamentals

A trademark protects the source identifier of goods or services — the brand signal to customers.

**Protectable elements:**
- Word marks (company name, product name, tagline)
- Design marks (logos)
- Trade dress (distinctive UI design, product packaging)
- Sound marks, color marks (rare, requires strong secondary meaning)

**Strength spectrum (strongest → weakest):**
```
Fanciful → Arbitrary → Suggestive → Descriptive → Generic

FANCIFUL: Made-up words with no prior meaning (Xerox, Kodak, Anthropic)
ARBITRARY: Real words unrelated to goods (Apple for computers)
SUGGESTIVE: Implies but doesn't describe (Netflix, Zoom)
DESCRIPTIVE: Describes the good/service (harder to protect)
GENERIC: The product name itself (cannot be trademarked)
```

### USPTO Registration Process

**Use-in-commerce filing:**
1. Trademark clearance search (USPTO TESS + common law)
2. File intent-to-use (ITU) or use-based application
3. Publication for opposition (30-day window)
4. Registration (if no opposition)
5. Maintenance: Section 8 declaration (5–6 years post-registration), Section 15 incontestability (5 years of continuous use), renewal every 10 years

**Timeline:** 12–18 months from filing to registration
**Cost:** $250–$350 per class (USPTO fees) + attorney fees

### Madrid Protocol (International Filing)

File in 100+ countries through a single international application via WIPO:

```
Base application → WIPO International Bureau → National offices
                   (1 application, 1 fee, 1 language)

Key markets for AI startups:
- US (USPTO) — primary market
- EU (EUIPO) — covers all 27 EU member states
- UK (IPO) — post-Brexit separate filing
- Canada (CIPO)
- Australia (IP Australia)
- China (CNIPA) — file early; first-to-file jurisdiction
```

**China trademark strategy:** File in China on day 1, not after you're successful there. Chinese bad-faith applications by trademark squatters are common.

---

## 5. Employee IP Agreements (PIIAs)

Every employee and contractor must sign an IP agreement before starting work.

### PIIA Core Provisions

**1. Assignment of inventions:**
```
All inventions, developments, works of authorship, and discoveries 
conceived, developed, or reduced to practice during employment, 
or within [6-12 months] after termination, relating to the company's 
business or using company resources, are assigned to the company.
```

**2. Pre-existing IP carveout:**
```
Employee discloses and the company acknowledges:
[List: personal projects, prior inventions not assigned to company]
Company waives rights to items on Exhibit A.
```

**3. California law carveout (Labor Code § 2870):**
California employees cannot be required to assign IP created:
- On their own time
- Without company resources
- NOT related to the company's business

**Critical:** Properly drafted PIIAs respect this; employees should list personal side projects before signing.

**4. Moral rights waiver:**
Where applicable (primarily for copyrightable works), employees waive moral rights (right of attribution, right of integrity).

**5. Confidentiality:**
Obligation to maintain confidentiality during and after employment (reasonable duration post-employment, often indefinite for trade secrets).

### Contractor vs. Employee IP

```python
# Contractor IP risk matrix
def assess_contractor_ip_risk(contractor_type: str) -> dict:
    if contractor_type == "employee":
        return {"risk": "low", "ownership": "company", "required": "PIIA"}
    
    elif contractor_type == "contractor_with_agreement":
        return {
            "risk": "low",
            "ownership": "company",
            "required": "Written assignment + NDA + PIIA equivalent"
        }
    
    elif contractor_type == "contractor_no_agreement":
        return {
            "risk": "HIGH",
            "ownership": "contractor",
            "required": "IMMEDIATE: get written assignment before delivery",
            "risk_note": "Contractor owns all code they write; you have a LICENSE only"
        }
    
    elif contractor_type == "offshore_contractor":
        return {
            "risk": "VERY HIGH",
            "required": "Local law IP assignment + US assignment",
            "risk_note": "Many jurisdictions give moral rights that can't be assigned"
        }
```

---

## 6. AI-Specific IP Considerations

### Model Weights: Ownership and Protection

Model weights are protectable as:
1. **Trade secrets** (strongest protection — if properly maintained)
2. **Copyright** (creative training process, though this is legally uncertain)
3. **Contract** (API terms of service, model licensing agreements)

**Model licensing landscape (2025–2026):**
- **Fully open** (MIT, Apache): Meta LLaMA 4 community license, Mistral
- **Open weights, restricted use**: some models prohibit commercial use or require attribution
- **Proprietary/API only**: Claude, GPT-4, Gemini 2 Ultra
- **Enterprise licensed**: negotiated terms with custom pricing

**Anthropic's Claude licensing:**
- Outputs from Claude: Anthropic's Terms allow customer ownership of outputs (with proper use)
- Fine-tuned Claude (via API): customers own their fine-tuned versions, Anthropic retains base rights
- Claude cannot be used to train competing models (per ToS)

### Training Data IP Risk

| Data Source | Copyright Risk | Recommended Use |
|---|---|---|
| Common Crawl | Medium (mixed content) | OK for pre-training, controversial for specific domains |
| Wikipedia | Low (CC-BY-SA) | OK, attribution required |
| GitHub Code | Medium | Varies by repo license; MIT/Apache safe, GPL risky |
| Books/papers | High without license | Need publisher license or fair use analysis |
| Proprietary databases | Very High | Need explicit license |
| Synthetic data | Low (if generated cleanly) | Safe if source model doesn't contaminate |

**Fair use analysis for AI training (ongoing litigation):**
The four fair use factors applied to AI training:
1. Purpose and character of use (transformative?)
2. Nature of the copyrighted work
3. Amount used
4. Effect on market for original

Courts are split; expect major rulings in 2025–2027. Risk mitigation: use licensed or public domain data where possible.

---

## 7. Data Licensing and Privacy IP

### Interplay Between IP and Privacy Law

Data as an asset sits at the intersection of:
- **Copyright**: original selection/arrangement of data
- **Trade secret**: proprietary datasets with maintained secrecy
- **Contract**: data sharing agreements, API terms
- **Privacy law**: GDPR, CCPA limit certain data uses regardless of IP ownership

**Data licensing framework for AI training:**
```
Level 1 — Public domain/CC0:
  Use freely, no restrictions, no attribution required

Level 2 — Creative Commons licensed:
  CC-BY: Attribution required
  CC-BY-SA: Share-alike (copyleft)
  CC-BY-NC: Non-commercial only — RISKY for commercial products

Level 3 — Open data licenses (ODbL, CDLA):
  Database-specific licenses, vary by terms

Level 4 — Proprietary licensed:
  Negotiate per-use or per-project licenses
  Key terms: field of use, exclusivity, sublicensing, AI training use
```

---

## 8. International IP Strategy

### Key Jurisdiction Considerations

**China:**
- First-to-file trademark system — register immediately upon entering Chinese market
- Strong patent enforcement for registered patents since 2019 reforms
- Model weight trade secret protection improving but still weaker than US/EU
- Software patents: possible through careful claim drafting

**EU:**
- Unitary Patent: single patent covering 17+ EU member states (effective 2023)
- GDPR affects training data use more than US law
- EU AI Act imposes new IP-adjacent requirements for AI systems

**UK (post-Brexit):**
- Separate filing now required (UK IPO)
- CIPA (Chartered Institute of Patent Attorneys) governed
- Computer programs "as such" not patentable (similar to EU but evolving)

**India:**
- Software patents: method must have technical effect beyond normal computer operation
- Large and growing talent pool; ensure PIIAs cover Indian IP law (which has different treatment)

### IP Due Diligence Checklist for Series A

```
Patents:
  □ List all filed/granted patents and applications
  □ Freedom-to-operate analysis for core product
  □ No outstanding patent disputes/litigation
  □ No inventor disputes (all inventors named, all assigned)

Copyright:
  □ All software code covered by employee/contractor PIIAs
  □ Open source inventory with license compliance verification
  □ Training data license documentation
  □ All works registered with Copyright Office (optional but strengthens damages)

Trade Secrets:
  □ Trade secret inventory document exists
  □ Access controls and confidentiality measures documented
  □ All employees/contractors signed NDAs + PIIAs
  □ No former employer IP contamination

Trademarks:
  □ Name and logo registered in primary markets (US, EU, priority markets)
  □ Domain name strategy (primary TLDs + typosquatting variants)
  □ Social media handles secured
  □ No infringement notices received

Contracts:
  □ All key agreements reviewed for IP ownership clarity
  □ Customer contracts: ownership of outputs clear
  □ Vendor contracts: no claims to your IP
```

---

## 9. Patent Defensive Strategy

### Defensive Publication

If a patent application would cost too much or be too hard to get, consider **defensive publication**: publish the invention publicly (e.g., in a technical blog, IP.com submission, or arXiv paper). This creates prior art that prevents anyone else from patenting it — including your competitors.

This is appropriate when:
- The invention is not core to competitive moat
- The cost/benefit of patent prosecution doesn't justify filing
- You want to prevent a competitor from patenting and blocking you

### Patent Pools and Cross-Licensing

For AI infrastructure patents, consider participating in:
- **Open Invention Network (OIN)**: cross-licenses Linux-related patents royalty-free
- **LOT Network**: license on transfer — if a member sells a patent, it can't be used against other members
- **Unified Patents**: collective defense against NPE/PAE (non-practicing entity / patent assertion entity) attacks

### Patent Troll Defense

NPEs (patent trolls) file demand letters against startups. Best practices:
1. Join the LOT Network (helps prevent NPE transfer attacks)
2. Maintain a patent clearance log for your product features
3. Use USPTO Inter Partes Review (IPR) to challenge invalid patents
4. Never respond to a demand letter without legal counsel
5. Consider patent litigation insurance (~$15K–$30K/year for a startup)

---

## 10. QSBS and IP (Section 1202 Intersection)

QSBS eligibility is affected by IP strategy:

**IP assignment is required for QSBS:**
- If founders hold IP outside the corporation at time of C-Corp formation, transfer to the corporation must happen before QSBS shares are issued
- IP transferred to the corporation must be properly documented (IP assignment agreement)
- The corporation must be an "active business" using the IP in operations

**Technology company active business test:**
- Software companies qualify under "technology" exception to QSBS active business test
- Consulting / professional services do NOT qualify
- Ensure your business description in corp documents reflects tech product focus, not services
