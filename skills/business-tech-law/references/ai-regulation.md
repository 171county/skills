# AI Regulation Reference

## EU AI Act

**Applicable law:** Regulation (EU) 2024/1689 of the European Parliament and of the Council. Published in the Official Journal June 12, 2024. Entered into force August 1, 2024.

**Extraterritorial scope:** Applies to providers placing AI systems on EU market or putting into service in EU, regardless of establishment. Also applies to deployers in the EU and providers/deployers whose AI output is used in the EU.

### Risk Classification Framework

The AI Act uses a tiered risk model. Every AI system must be classified before deployment:

#### Tier 1: Unacceptable Risk — Prohibited (Art. 5)

AI systems that pose an unacceptable risk to fundamental rights and safety are banned outright. **Compliance deadline: February 2, 2025.**

Prohibited practices include:
- Subliminal, manipulative, or deceptive techniques that impair rational decision-making
- Exploitation of vulnerabilities of specific groups (age, disability, socioeconomic situation)
- Social scoring by public authorities (evaluating people based on behavior/personal characteristics)
- Real-time remote biometric identification in public spaces by law enforcement (narrow exceptions for specific serious crime investigations)
- Biometric categorization systems inferring sensitive attributes (race, political opinions, religious beliefs, sex life) from biometric data
- Predictive policing based solely on profiling individuals
- Emotion recognition in workplaces and educational institutions (subject to limited exceptions)
- Untargeted scraping of facial images from internet/CCTV to build facial recognition databases

**Penalty for violations:** Up to €35M or 7% of global annual turnover, whichever is higher.

#### Tier 2: High-Risk AI Systems (Arts. 6, 9–16)

Systems subject to conformity assessment before deployment and ongoing obligations. **Full compliance deadline: August 2, 2026** (Annex I embedded in regulated products: August 2, 2028; Annex III systems like recruitment, credit, law enforcement: December 2, 2027).

**Annex I — Safety components in regulated products:** Medical devices, machinery, vehicles, aviation, toys — AI components in these products follow both the AI Act and the product-specific regulation.

**Annex III — Standalone high-risk AI systems** (categories):
1. **Biometric systems** — categorization, emotion recognition (with exceptions)
2. **Critical infrastructure** — management/operation of road traffic, water, gas, electricity, digital infrastructure
3. **Education and vocational training** — AI determining access/outcomes (admissions, grades, competencies)
4. **Employment and workers** — recruitment/selection tools, performance evaluation, promotion/termination decisions, task allocation
5. **Access to private/public services** — creditworthiness assessment, risk scoring for life/health insurance, emergency service dispatch
6. **Law enforcement** — risk assessment for criminality, polygraphs, evidence reliability assessment, crime analytics
7. **Migration and border control** — risk assessment of persons, document authentication
8. **Justice and democratic processes** — application of law, dispute resolution, electoral campaign influence

**Obligations for high-risk AI providers:**
- Establish Quality Management System (QMS) (Art. 17)
- Technical documentation (Art. 11) — training data, validation, model architecture, accuracy, robustness, cybersecurity
- Logging and record-keeping throughout system lifecycle (Art. 12)
- Transparency and instructions for use (Art. 13) — deployers must be able to implement human oversight
- Human oversight measures (Art. 14) — system must enable operators to understand, monitor, override, or shut down
- Accuracy, robustness, and cybersecurity (Art. 15)
- Register in EU database before deployment (Art. 71)
- Conformity assessment (Art. 43) — self-assessment or third-party assessment depending on category
- Post-market monitoring plan (Art. 72)
- Incident reporting to national authorities (Art. 73) — serious incidents and malfunctions

**Obligations for high-risk AI deployers:**
- Use systems in accordance with instructions for use
- Assign human oversight to competent persons
- Monitor for serious incidents and malfunctions; report to provider
- Conduct fundamental rights impact assessment for Annex III systems in public services
- Inform employees when AI is used for workplace monitoring/decisions
- Log keeping for at least 6 months

#### Tier 3: Limited Risk (Art. 52 Transparency Obligations)

AI systems posing limited risk have transparency obligations:
- **Chatbots:** Must inform users they are interacting with an AI (except obvious entertainment/artistic contexts)
- **AI-generated content (deepfakes):** Must be labeled as artificially generated/manipulated, particularly for real people
- **Emotion recognition / biometric categorization systems:** Must inform persons exposed
- **AI-generated text on matters of public interest:** Publishers must label where AI substantially generated the text (with narrow exception for editorial review)

#### Tier 4: Minimal Risk

The vast majority of AI applications: spam filters, AI in video games, inventory management, AI-assisted drafting tools. No specific obligations under the AI Act beyond AI literacy requirement.

### General-Purpose AI Models (GPAI) (Arts. 51–56)

**Compliance deadline: August 2, 2025.**

GPAI models (including foundation models like GPT, Claude, Gemini, Llama) have separate obligations:

**All GPAI providers must:**
- Draw up and keep up-to-date technical documentation (training data, evaluation results, capabilities, limitations)
- Provide information/documentation to downstream providers for compliance
- Establish copyright compliance policy
- Publish summary of training data used (with copyright transparency)

**GPAI with systemic risk (≥10^25 FLOPs training compute, or EC designation):**
- Perform model evaluations, including adversarial testing
- Track, document, and report serious incidents to EC
- Ensure cybersecurity protections
- Report on energy consumption

---

## US AI Regulation Landscape

The US has no single comprehensive federal AI law as of mid-2026. Regulation is sector-specific and agency-driven.

### FTC AI Guidance

The FTC regulates AI primarily through existing authority under FTC Act § 5 (unfair or deceptive acts or practices) and specific statutes (FCRA, COPPA, etc.).

**FTC enforcement priorities:**
- **AI-washing:** Deceptive claims about AI capabilities (e.g., claiming "AI-powered" when functionality is minimal). FTC 2024 examination priorities identified this as a top concern.
- **Deceptive/unfair automated decisions:** AI-driven decisions in credit, employment, housing that discriminate or use deceptive methods
- **Biometrics:** FTC Policy Statement on biometric information (2023) — enforcement for collecting biometric data without consent, using for discriminatory purposes
- **Privacy violations:** AI products that misuse personal data in ways inconsistent with representations

**FTC Guidelines for AI (non-binding but signals enforcement):**
- Be transparent about AI use and limitations
- Explain the basis for AI-driven decisions that affect consumers
- Ensure AI systems are accurate and not discriminatory
- Protect privacy of data used to train and run AI systems

### NIST AI Risk Management Framework (AI RMF 1.0)

Published January 2023; **voluntary but increasingly referenced by regulators.** Updated via:
- NIST AI 600-1 (July 2024): Generative AI Profile — 12 risk categories for GenAI including confabulation, data privacy, information integrity, IP, data poisoning, homogenization
- NIST IR 8596 (December 2025 preliminary draft): Cybersecurity Framework Profile for AI — bridges AI RMF with CSF 2.0

**AI RMF Core Functions:**
1. **GOVERN** — Organizational policies, culture, and accountability structures for AI risk
2. **MAP** — Identify and categorize AI risks in context
3. **MEASURE** — Analyze and assess AI risks (bias testing, explainability, robustness evaluation)
4. **MANAGE** — Prioritize, respond to, and monitor AI risks

**Why it matters legally:** The FTC, CFPB, EEOC, FDA, and SEC all reference NIST AI RMF principles in their guidance. SOC 2 Type II reports are increasingly expected to include AI trust service criteria aligned with AI RMF. ISO/IEC 42001:2023 (AI Management System Standard) crosswalks to AI RMF.

### New York City Local Law 144 (Automated Employment Decision Tools)

**Effective:** January 1, 2023; enforcement began July 5, 2023. **Enforced by:** NYC Department of Consumer and Worker Protection (DCWP).

**Scope:** Employers and employment agencies operating in NYC that use Automated Employment Decision Tools (AEDTs) — data analytics, ML, and AI that generate scores, classifications, or recommendations used in hiring or promotion decisions.

**Key requirements:**
1. **Annual bias audit** by an independent third-party auditor within 12 months preceding each use; audit must assess disparate impact on sex, race/ethnicity
2. **Public disclosure** of bias audit summary on company website (at least until the next audit)
3. **Notice to candidates** at least 10 business days before use of AEDT; must include description of the tool's use and the job qualifications/characteristics it evaluates
4. **Opt-out alternative:** Candidates must be informed of and offered an alternative selection process that does not use the AEDT

**Penalties:** $500–$1,500 per day per violation. DCWP enforcement has been relatively limited so far (Dec. 2025 audit found disclosure gaps) but enforcement is expected to intensify.

**Practical impact:** Any startup or enterprise using AI-powered ATS (Applicant Tracking Systems), résumé screeners, or interview assessment tools for NYC hires must comply. Major vendors (HireVue, Pymetrics, etc.) have published their bias audit results. Companies using these tools must ensure vendors have completed audits and provide required notices.

### Sector-Specific AI Regulation

**FDA — AI in Medical Devices (SaMD):**
- AI/ML-based Software as a Medical Device regulated under 21 U.S.C. § 360 (FD&C Act)
- Total Product Lifecycle (TPLC) approach from FDA's January 2025 draft guidance
- **Predetermined Change Control Plans (PCCP):** FDA December 2024 final guidance allows manufacturers to pre-approve AI software update pathways without new submissions for covered changes
- Key submission elements: model description, training/validation data, performance tied to claims, bias analysis, human-AI workflow, monitoring plan
- As of July 2025, 1,250+ AI-enabled medical devices authorized in the US

**SEC — AI in Financial Services:**
- SEC proposed rules on "Conflicts of Interest Associated with the Use of Predictive Data Analytics" (2023) were withdrawn in June 2025
- SEC 2025 examination priorities focus on: AI-washing by investment advisers, adequacy of AI-related policies and procedures, supervision of AI use
- SEC launched AI Task Force (August 2025)
- FTC Act § 5 applies to deceptive AI claims; SEC's existing fraud framework (17 CFR § 240.10b-5) applies to securities fraud via AI manipulation
- FINRA Rule 2010 (standards of commercial honor) applied to AI-driven suitability recommendations

**CFPB — AI in Consumer Credit:**
- Adverse Action Notices (15 U.S.C. § 1691 et seq.; ECOA Reg B) must include specific reasons for AI-driven credit denials
- "Black box" explanations are insufficient — must identify the actual reason(s)
- CFPB Circular 2022-03: FCRA accuracy obligations apply to AI-generated credit recommendations

---

## AI Product Compliance Checklist

For a US-based company deploying AI products:

**Pre-Launch:**
- [ ] Classify system under EU AI Act risk tiers (if EU market)
- [ ] Conduct NIST AI RMF risk mapping
- [ ] Complete DPIA under GDPR (if processing EU personal data in AI)
- [ ] Conduct CPPA risk assessment if using ADMT on CA consumers (effective Jan 1, 2026)
- [ ] Implement bias testing; document results
- [ ] Prepare transparency disclosures (EU AI Act Art. 52; NYC LL144 if applicable)
- [ ] Draft AI-specific clauses in ToS (accuracy disclaimer, prohibited uses, human review option)

**Ongoing:**
- [ ] Annual bias audits (NYC LL144; good practice generally)
- [ ] Monitor for GPAI/foundation model compliance obligations if applicable
- [ ] Track EU AI Act high-risk obligations timeline (Aug 2026 for most high-risk systems)
- [ ] Log and review AI system decisions (retention period: 6 months minimum under EU AI Act Art. 12)
- [ ] Maintain incident response plan for AI system failures
- [ ] Update technical documentation when model changes materially
