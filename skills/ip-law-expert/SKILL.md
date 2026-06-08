---
name: ip-law-expert
description: Expert-level IP law for technology companies — software patents (Alice test, USPTO 2025 guidance), copyright (code, open source licenses, AGPL risks), trade secrets (DTSA, PIIA requirements), trademarks (clearance, registration, Madrid Protocol), AI-specific IP issues (Thaler v. Perlmutter settled law, NYT v. OpenAI, Getty v. Stability AI), and IP strategy for fundraising and M&A. Use when advising on IP protection strategy, open source compliance, patent filing decisions, trademark registration, or AI training data legal risk.
---

# IP Law Expert

> **Disclaimer**: This skill provides legal information, not legal advice. For specific legal matters, consult qualified IP counsel.

## Software Patents

### The Alice Test (Current Standard)

**Alice Corp. v. CLS Bank International (2014 Supreme Court)**
Alice test for software patent eligibility under 35 U.S.C. § 101:

**Step 1**: Is the claim directed to a process, machine, manufacture, or composition of matter?
- Almost always yes for software → proceed to Step 2

**Step 2A (Two-Prong)**:
- **Prong 1**: Is the claim directed to an abstract idea, law of nature, or natural phenomenon?
  - Abstract ideas include: mathematical concepts, certain methods of organizing human activity, **mental processes**
  - "Mental process" = concept performed in the human mind (includes mental steps that could theoretically be performed mentally)
  
- **Prong 2**: Does the claim integrate the abstract idea into a practical application?
  - Look for: improvement to technology itself, specific technical implementation, unconventional use of computer components
  - **NOT sufficient**: generic computer implementation of abstract idea; "apply it" with a computer

**Step 2B** (if directed to abstract idea without practical application):
- Does the claim add "significantly more" than the abstract idea itself?
- "Significantly more" = unconventional technical solution that amounts to more than well-understood, routine, conventional activity

### USPTO August 2025 Guidance — Material Shift

**Key Changes (Guidance Memo August 2025)**:
1. **Narrowed "mental process" exclusion**: Examiners must show claims could ACTUALLY be performed by a human mentally — purely theoretical mental performance insufficient to reject
2. **AI training presumptively non-abstract**: AI model training claims (backpropagation, gradient descent) presumptively have practical application; fewer § 101 rejections expected
3. **"More likely than not" standard**: Raises bar for rejection — examiner must affirmatively show abstract idea before rejecting
4. **Technical improvement emphasis**: Strengthen claim language around concrete technical improvements

**Practical Impact**: Previously ~60-70% of software patent applications received § 101 rejections; post-guidance trend suggests meaningful improvement in allowance rates.

### Patent Strategy for Startups

**Provisional Application**
- Cost: $320-$1,600 (micro/small/large entity)
- Benefits: establishes priority date; 12 months to convert to non-provisional
- No formal claims required; detailed description sufficient
- **File early**: patent rights based on first-to-file (since AIA 2013)
- File before any public disclosure, product launch, or funding announcement

**Non-Provisional Application**
- Cost: $10,000-$25,000+ with attorney
- Prosecution timeline: 18-36 months to first office action
- Total timeline to grant: 3-5 years average
- Continuation strategy: file continuation before parent grants to extend prosecution and expand claims

**Patent Portfolio Strategy**
- File defensively: 3-5 core patents per product line minimum
- Focus on: novel algorithms, data processing methods, system architectures, user interaction innovations
- Avoid: purely business methods (high § 101 risk), trivial UI elements
- **QSBS IP stacking**: early patent filing + each distinct patent family = separate basis for QSBS qualification analysis

**Inter Partes Review (IPR) — PTAB**
- Post-grant validity challenge mechanism
- PTAB institution rate collapsed: from ~75% (2015 peak) → ~35% (2024-2025) → some sources cite 24.6% against NPEs
- America Invents Act § 315(b): 1-year time bar from complaint service
- Cost: $100K-$300K to institute and complete
- **Petitioner advantage eroded**: PTAB increasingly reluctant to institute; strong patents more defensible

---

## Copyright Law

### Code Copyright

**What's Protected**
- Source code: protected as literary work from creation (no registration required for protection)
- Registration: required before filing infringement suit; enhanced damages for timely registration
- **SSO (Structure, Sequence, and Organization)**: protectable beyond literal copying
  - Oracle v. Google (2021): API declaratory code protected; but Google's use = fair use (unanimous SCOTUS)
  - **Lesson**: APIs can be copyrighted; implementing compatible API is legally complex

**What's Not Protected**
- Ideas, algorithms, mathematical formulas (only expression, not idea)
- Functional elements dictated by efficiency (merger doctrine)
- Standard program elements (scènes à faire)
- Pure functionality

**Work Made for Hire**
- Employee work: company owns copyright automatically
- **Contractor gap**: work by independent contractors is NOT work-for-hire unless:
  1. Written agreement signed BEFORE work begins
  2. Work falls in one of 9 statutory categories (software qualifies as "compilation")
  - **Critical**: verbal agreements insufficient; must be written
- Solution: PIIA (below) covers contractors separately

**AI-Generated Code**
- No copyright protection for purely AI-generated code (Thaler v. Perlmutter, settled law as of March 2026)
- Human authorship required; human must make "sufficient creative choices"
- **Practical guidance**: document human creative input in AI-assisted development; maintain logs of prompts, edits, selections made by humans
- Copyright registration: disclose AI use; register human-authored elements

### Open Source License Matrix

| License | Copyleft | SaaS Trap | Patent Grant | Commercial Use | Best For |
|---------|----------|-----------|-------------|----------------|---------|
| MIT | None | No | No (no explicit) | Yes | Maximum permissiveness |
| Apache 2.0 | None | No | Yes (explicit) | Yes | Corporate-friendly, patent safety |
| GPL v2 | Strong | No | No | Yes (see terms) | Traditional copyleft |
| GPL v3 | Strong | No | Yes | Yes (see terms) | Modern copyleft |
| LGPL v2.1/3 | Weak (library) | No | Yes (v3) | Yes | Library distribution |
| AGPL v3 | **Network** | **YES** | Yes | Yes (see terms) | Copyleft for SaaS |
| BSL (BUSL) | Time-limited | Varies | No | Restricted | Source-available model |
| CC0 | None | No | No | Yes | Data, content only |

**AGPL Trap for SaaS Companies**
- AGPL § 13: if you run AGPL-licensed software as a network service, you must provide complete source to users
- "Network service" = API, SaaS application, any web-accessible software
- Dependencies: if any component in your stack is AGPL, your entire application may need AGPL disclosure
- **Real-world risk**: using AGPL-licensed component (e.g., some AI models, databases) in SaaS = must open-source your application
- **Solution**: commercial license from AGPL licensor (common dual-licensing model: MongoDB, Elastic)

**Copyleft Contamination Analysis**
```
Build dependency tree
For each dependency:
├── GPL/AGPL? → check linking (static vs dynamic linking debate ongoing)
├── AGPL in network service? → disclosure required for all users
├── GPL in distributed binary? → distribution triggers copyleft
└── LGPL? → generally safe with dynamic linking; check per license version
```

---

## Trade Secrets

### Defend Trade Secrets Act (DTSA, 2016)

**Federal Protection** (first federal civil trade secret law)
- Elements: (1) trade secret exists, (2) reasonable measures to maintain secrecy, (3) misappropriation
- Remedies: injunctions, damages, exemplary damages (2x for willful), attorney fees
- Ex parte seizure: rare, high bar — emergency remedy for imminent disclosure

**What Qualifies as Trade Secret**
- Any information with independent economic value from being secret
- Technical: algorithms, source code, training data/models, architecture docs
- Business: customer lists, pricing strategies, supplier relationships, sales data
- Financial: unreleased performance data, business plans

**Reasonable Measures Requirements (Critical)**
Physical measures:
- Access-controlled facilities; visitor logs; clean desk policy

Digital measures:
- Access controls: least privilege; MFA for sensitive systems
- Code repositories: private by default; access logs
- DLP (Data Loss Prevention): tools to detect/prevent data exfiltration
- NDAs with all personnel with access
- Watermarking/canary tokens for highly sensitive documents

Contractual measures:
- NDA with all employees, contractors, vendors, investors (with access)
- IP Assignment Agreement (PIIA) at hiring
- Non-disclosure obligations that survive termination

Operational measures:
- Need-to-know basis; compartmentalization
- Employee training on trade secret obligations
- Offboarding procedures: credential revocation, equipment return, exit interview

**Failure to maintain reasonable measures = trade secret loses protection entirely**

---

## Employee/Contractor IP Agreements

### PIIA (Proprietary Information and Inventions Assignment)

**Core Provisions**
1. **Assignment clause**: assigns all IP created during employment to company
   - Must use **present-tense assignment** language: "I hereby assign" (not "I agree to assign")
   - "I agree to assign" = obligation requiring future action; "I hereby assign" = automatic transfer
   - Critical difference upheld in Filmtec Corp. v. Allied Signal (Fed. Cir.)
   
2. **Disclosure obligation**: employee must disclose all inventions created during employment

3. **Work-made-for-hire confirmation**: even with automatic assignment, belt-and-suspenders confirmation

**State Carve-Outs (Mandatory Exceptions)**

California (Lab. Code §§ 2870-2872):
- Employee retains IP if: (1) no company equipment/facilities, (2) no company trade secrets used, (3) doesn't relate to company business OR reasonably anticipated R&D
- Disclosure: must give notice of §2870 exception in PIIA
- **Practice**: most PIIAs include the statutory exception language

Delaware, Illinois, Minnesota, North Carolina, Washington:
- Similar statutory carve-outs; each state has specific language requirements
- File PIIA under state where employee works, not where company is incorporated

**Moonlighting Provisions**
- Employee's after-hours work that competes or uses company knowledge = gray area
- PIIA should require disclosure of outside work; non-compete (where enforceable) limits competition
- California: non-competes for employees void under Bus. & Prof. Code § 16600 (except limited exceptions for business sale)

**FTC Non-Compete Rule Status**
- Biden-era FTC rule banning most non-competes: vacated by 5th Circuit (August 2024); formally removed February 2026
- **Current status**: non-competes governed entirely by state law
- Enforceable (limited): TX, FL, MA (with restrictions), NY (for senior executives post-2024 amendment)
- Not enforceable: CA, ND, OK, MN (2023 joined CA)

---

## Trademark Law

### Trademark Clearance Process

**Level 1: Knockout Search**
- USPTO TESS (Trademark Electronic Search System): free
- Search: exact marks, phonetically similar marks, similar goods/services
- 5-10 minutes; eliminates obvious conflicts

**Level 2: Comprehensive Search**
- Trademark attorney + Thomson CompuMark or similar service ($500-$2,000)
- Covers: USPTO federal marks, state registrations, common law unregistered marks, domain names, international
- Output: clearance opinion letter

**Level 3: Full Availability Opinion**
- Broad common law search; social media, app stores, business name registrations
- $2,000-$5,000 attorney opinion
- Recommended before significant brand investment

**DuPont Factors (Likelihood of Confusion Standard)**
- 13 factors; key factors:
  1. Similarity of marks (sound, appearance, meaning)
  2. Relatedness of goods/services
  3. Strength of senior mark
  4. Actual confusion evidence
  5. Sophistication of consumers
- Even 2 similar-sounding marks in similar industries = likely conflict

### USPTO Registration Process

**§ 1(a) Use-Based Application**
- Requires: mark already in commerce in connection with specific goods/services
- First use dates documented; attach specimen (screenshot, product photo)
- Timeline: 8-18 months to registration (if no office actions)

**§ 1(b) Intent-to-Use Application**
- File before using mark; preserves priority date
- Extension of time to use: 6 months initial + 5 × 6-month extensions = 3 years total
- Statement of Use filed when actual use begins

**Key International Classes for Tech Companies**
- **Class 9**: Computer software, mobile applications, downloadable software, electronics
- **Class 35**: Business services, SaaS platforms, marketing/advertising services, data analysis
- **Class 38**: Telecommunications, messaging platforms, streaming, API services
- **Class 42**: SaaS, cloud computing, software development, cybersecurity, research services

**Registration Strategy**
- File in each class where used/planned; each class = separate fee ($250-$350 per class)
- File early: ®️ rights back-dated to application date
- Monitor and enforce: file §8 declaration (between years 5-6) and §9 renewal (every 10 years)

### Madrid Protocol (International Trademark)

**Overview**
- Single international application covers 130+ member countries
- Filed through WIPO (World Intellectual Property Organization)
- Must have "basic mark" in home country (US application or registration)

**Advantages**
- Single filing fee ≈ $2,500-$5,000 (vs. $50,000+ for individual national filings)
- Centralized management; single point of renewal

**July 2025 Update**: WIPO now accepts partial replacement (designating specific Nice classes per country, not all)

**Critical Risk: Central Attack**
- If base US application/registration cancelled within 5 years → all international designations fall
- **Mitigation**: ensure US registration is solid before relying on Madrid Protocol chain

---

## AI-Specific IP Issues

### AI Authorship — Settled Law

**Thaler v. Perlmutter (Thaler v. Vidal)**
- Timeline: Thaler attempted to register AI-generated artwork naming DABUS (AI) as author
- D.C. Circuit (March 2025): affirmed human authorship required; AI cannot be listed as author
- SCOTUS denied cert (March 2026): **settled law as of March 2026**
- **Implication**: AI-generated works without human creative authorship are in the public domain
- **Practical**: document every human creative decision in AI-assisted development

### AI Training Data Litigation

**New York Times v. OpenAI (Ongoing)**
- Filed December 2023; discovery ongoing as of 2026
- Court ordered 20M log sample of training outputs
- Key issue: direct copying of articles verbatim by GPT models
- **Potential ruling impact**: defines whether LLM training = copyright infringement

**Getty Images v. Stability AI (UK)**
- UK High Court, November 2025 ruling: training data copying = copyright infringement under UK law
- Secondary infringement (UK) and direct copying of watermarked images found
- Image output claims rejected (output not similar enough to specific Getty images)
- **US implications**: UK ruling not binding but signals judicial direction; US case pending

**Risk Matrix for AI Training**
| Data Source | Risk Level | Notes |
|-------------|-----------|-------|
| Licensed data (negotiated) | Low | Ensure license covers training |
| Public domain (CC0, pre-1928) | Low | Verify public domain status |
| Creative Commons (CC-BY, CC-BY-SA) | Medium | Attribution + share-alike requirements apply to outputs? |
| Web-scraped content | High | NYT/Getty cases; no clear safe harbor |
| Proprietary company data | Requires IP clearance | PIIA coverage; third-party data licenses |
| User-generated content | Medium-High | Platform ToS must include training rights |

---

## IP Red Flags in Due Diligence

**M&A / Fundraising IP Checklist**

Critical red flags:
- [ ] Missing PIIA from any co-founder (ownership dispute risk)
- [ ] Contractor IP not assigned (missing written agreement)
- [ ] Open source AGPL components in commercial SaaS (disclosure obligation)
- [ ] Training data licenses don't permit model training/commercial use
- [ ] Patent pending applications with § 101 rejections not overcome
- [ ] Missing trademark clearance on brand name in key markets
- [ ] Non-compete agreements with prior employers potentially covering current work
- [ ] Patent cross-licensing obligations from previous employer
- [ ] Uncleared university IP (joint inventor from PhD/postdoc work)

**University IP Issues**
- Bayh-Dole Act: universities own IP created with federal funding
- If founder was grad student/postdoc: prior university's tech transfer office may claim rights
- Solution: get written IP clearance from university before founding or licensing
- **Red flag**: founders who left university lab and immediately founded company using lab technology

---

## Expert Calibration Notes

- **PIIA language "hereby assign" vs "agree to assign"** is a $1M+ distinction that has been litigated repeatedly — review every contractor agreement for this distinction
- **AGPL in your dependency tree is a hidden liability**: run automated license scanning (FOSSA, TLDR Legal, Licensee) on every dependency before each release
- **August 2025 USPTO guidance materially shifts the § 101 calculus**: previously rejected AI training method claims deserve re-examination with new guidance in hand
- **Madrid Protocol central attack risk is underappreciated**: build independent national filings in top-5 markets (US, EU, UK, China, Japan) rather than relying entirely on international registration chain
- **Thaler v. Perlmutter is settled**: stop spending time on AI authorship debate — document human creative contributions and file standard applications
- **Trade secret protection lives or dies on "reasonable measures"**: a company that has good code but no NDAs, no access controls, and no employee training has no trade secrets legally — the protection must be built before the secret is needed
