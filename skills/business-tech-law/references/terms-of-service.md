# Terms of Service and Acceptable Use Reference

## ToS Architecture for Tech/AI Products

Consumer-facing tech products typically deploy this document stack:

1. **Terms of Service / Terms of Use** — governs the user relationship; sets rights and obligations
2. **Privacy Policy** — discloses data handling (legally required; not just best practice)
3. **Acceptable Use Policy (AUP)** — defines prohibited conduct; incorporated by reference into ToS
4. **Cookie Policy** — required by GDPR/ePrivacy Directive; often embedded in Privacy Policy
5. **AI-Specific Addendum** — increasingly common for AI products; disclaims AI accuracy, defines AI output ownership, sets AI-specific prohibitions

**Binding mechanics:** ToS is only enforceable if users are on notice and assent. The law distinguishes:
- **Clickwrap (enforceable):** User must affirmatively check a box or click "I agree" — courts consistently enforce
- **Browsewrap (enforceable with caveats):** Notice of terms on site; user bound by use — courts require conspicuous notice (hyperlink must be near the action button, above the fold)
- **Scrollwrap (intermediate):** User must scroll through terms before accepting — generally enforceable

*Meyer v. Uber Technologies (2d Cir. 2017)*; *Nguyen v. Barnes & Noble (9th Cir. 2014)*: Leading cases on notice requirements. Prominent placement and clear language of the ToS link near the registration/purchase button is the minimum bar.

---

## Key ToS Clauses for AI Products

### 1. Disclaimer of AI Accuracy

This is non-negotiable for any product powered by generative AI:

**Essential elements:**
- AI outputs may be inaccurate, incomplete, offensive, or misleading
- Do not rely on AI outputs for legal, medical, financial, or safety-critical decisions without independent verification
- The company does not warrant the accuracy, completeness, or fitness for any particular purpose of AI-generated content
- AI outputs are not professional advice of any kind

**Why it matters:** Without clear disclaimers, users may argue reliance on AI outputs gives rise to professional liability claims (unauthorized practice of law, medicine, financial advice). Courts are still developing standards for AI-generated content liability, but clear contractual disclaimers are the primary defense.

**Note:** Disclaimers do not shield against FTC Section 5 claims for deceptive AI-washing. If you market accuracy, you are held to that representation.

### 2. AI Output Ownership

**Standard industry position (as of 2026):** Provider disclaims ownership of outputs; assigns to user. Major providers (Anthropic, OpenAI, Google) follow this model.

**Clauses to include:**
- "Subject to your compliance with these Terms, we assign to you all rights we may have in AI-generated outputs that you request"
- "You are responsible for any outputs you share or publish"
- Carve-out: provider retains non-exclusive rights to use de-identified queries/outputs for safety monitoring and model improvement (requires separate disclosure and, for EU users, lawful basis under GDPR)

**Copyright caveat:** US Copyright Office has issued guidance (2023, 2024) that AI-generated works without human authorship are not copyrightable. Works with sufficient human creative input can be copyrighted. Inform users: outputs may not be independently protectable as copyright works.

### 3. Prohibited Uses (AUP for AI Products)

A well-drafted AUP for an AI product must explicitly prohibit:

**Always-prohibited:**
- Generating CSAM (child sexual abuse material) — 18 U.S.C. § 2256 creates criminal liability
- Using AI to develop weapons of mass destruction (chemical, biological, nuclear, radiological)
- Circumventing safety filters or jailbreaking the AI system
- Training competing models on service outputs without express authorization
- Generating content to impersonate individuals in a deceptive manner (deepfakes used to defraud)

**Prohibited except for authorized use cases:**
- Automated decision-making in consequential domains (credit, hiring, insurance) without human oversight
- Mass generation of political misinformation
- Targeting protected classes in advertising (ECOA, FHA, Civil Rights Act implications)
- Processing special category personal data (GDPR Art. 9) without appropriate safeguards

**Industry-specific prohibitions:**
- For fintech/investment tools: express prohibition on reliance for investment decisions without human advisor review
- For health tools: express prohibition on use as a substitute for professional medical advice, diagnosis, or treatment
- For legal tools: express prohibition on reliance as a substitute for licensed legal counsel

### 4. Limitation of Liability for AI Products

Standard LoL clauses (indirect damages exclusion; fee-based cap) are particularly important for AI products because the potential downstream harm from AI reliance is large and unpredictable.

**Additional AI-specific LoL elements:**
- Exclude liability for decisions made based on AI-generated outputs
- Exclude liability for user-created prompts that elicit harmful content
- No warranty of fitness for any particular purpose (especially professional advice contexts)

**Note:** LoL clauses do not protect against: (a) intentional misconduct or fraud, (b) consumer protection law violations, (c) COPPA violations, (d) claims where a court finds the clause fails of its essential purpose.

---

## Arbitration and Class Action Waivers

### Federal Arbitration Act (9 U.S.C. § 1 et seq.)

Federal policy strongly favors arbitration. The FAA requires courts to enforce written arbitration agreements according to their terms, with narrow exceptions (fraud, unconscionability, delegation of threshold questions to arbitrator).

**Standard B2C arbitration clause elements:**
- Mandatory individual arbitration for all claims (contract, tort, statute) arising from or related to service use
- Class action waiver — all claims must be brought individually
- JAMS or AAA rules (specify; include incorporated rules by reference or link)
- Seat and governing law
- Opt-out clause: provide 30-day window to opt out of arbitration; this improves enforceability in courts that scrutinize arbitration adhesion contracts
- Carve-out for small claims court; carve-out for injunctive/declaratory relief (IP protection)

**Class action waiver enforceability post-Concepcion:**
- *AT&T Mobility LLC v. Concepcion* (2011): FAA preempts state unconscionability rules that specifically target class action waivers in arbitration clauses
- *Epic Systems Corp. v. Lewis* (2018): Extended Concepcion to employment arbitration agreements — employees can be required to waive class action rights for labor disputes
- **2024 Ninth Circuit:** *Heckman v. Live Nation* — court found specific mass-arbitration procedure unconscionable under California law; FAA did not preempt. Lesson: the arbitration provider and procedure must be fair, not just technically compliant.

**Mass arbitration risk:** After plaintiffs' bar developed mass arbitration tactics (filing thousands of individual claims simultaneously), the AAA amended its Mass Arbitration Supplementary Rules (2024) and JAMS implemented similar procedures (May 2024). These allow batching and bellwether proceedings. Companies must review whether their arbitration clause inadvertently exposes them to mass arbitration risk more dangerous than class action.

**California PAGA:** After *Viking River Cruises v. Moriana* (2022), individual PAGA claims can be compelled to individual arbitration, but representative PAGA claims for other employees may not be waivable. California employers should evaluate PAGA exposure separately.

### Drafting Tip: Modifying ToS

Unilateral modification clauses ("We may update these Terms at any time; continued use constitutes acceptance") are generally enforceable for future claims but not for vested rights. Best practice:
- Provide notice of material changes via email or in-product notification
- Specify effective date (at least 30 days ahead for material changes)
- Include opt-out mechanism for users who reject changes (i.e., ability to close account)

---

## DMCA Safe Harbor (17 U.S.C. § 512)

The DMCA safe harbor protects online service providers (OSPs) from copyright infringement liability for user-generated content, provided the OSP meets specific conditions.

### Safe Harbor Categories and Requirements

**Section 512(c) — Storage of User-Posted Content (most relevant for platforms):**

To qualify, the OSP must:
1. **Not have actual knowledge** of infringing activity
2. **Not be aware of facts or circumstances** from which infringing activity is apparent ("red flag knowledge")
3. **Not receive a financial benefit** directly attributable to the infringing activity (when OSP has right and ability to control)
4. **Respond expeditiously** to valid DMCA takedown notices
5. **Designate an agent** to receive takedown notices; register agent with US Copyright Office (failure to register = loss of safe harbor)
6. **Adopt and implement a repeat infringer policy**

**512(d) — Linking (for platforms linking to infringing content):** Same conditions as 512(c).

**512(a) — Transitory communications (ISPs):** No knowledge or control requirement; automatic for passive conduits.

### DMCA Takedown Process (17 U.S.C. § 512(c)(3))

Valid takedown notice must include:
- Physical or electronic signature of copyright owner or authorized agent
- Identification of the copyrighted work claimed to be infringed
- Identification of the material claimed to be infringing (URL or specific location)
- Contact information of complainant
- Good faith statement that use is not authorized
- Statement under penalty of perjury that the information is accurate

Upon receipt of a valid notice, the OSP must: (1) expeditiously remove or disable access to the material, (2) promptly notify the subscriber, (3) replace the material if a valid counter-notification is received after 10–14 business days.

### DMCA + AI: Evolving Law

The DMCA safe harbor was designed for passive hosts of user-generated content. Generative AI companies face distinct challenges:

- **Training data scraping:** Courts have held that scraping publicly available content for AI training may not be DMCA-exempt; see *Andersen v. Stability AI* (pending); multiple ongoing litigations
- **Infringing outputs:** If AI generates content substantially similar to copyrighted works, the OSP/AI provider may not have DMCA protection for those outputs because the provider is the "creator" not just a host
- **Second Circuit reaffirmation (Jan. 2025):** *Capitol Records v. Vimeo* — reaffirmed DMCA safe harbor for user-generated content but highlighted that AI-generated content raises different questions

**Bottom line:** DMCA safe harbor provides meaningful protection for platforms hosting *user-created* AI outputs, but providers of AI systems that directly generate infringing content face substantial copyright exposure not clearly covered by § 512. Expect significant litigation and regulatory development in this area through 2026–2028.

---

## Section 230 (47 U.S.C. § 230)

Section 230(c)(1): "No provider or user of an interactive computer service shall be treated as the publisher or speaker of any information provided by another information content provider."

### What Section 230 Protects

- **Good Samaritan provisions (§ 230(c)(2)):** Protects platforms' editorial discretion in content moderation — removing content in good faith even if that removal would otherwise create liability
- **Classic protection:** Platform is not liable for defamatory, tortious, or otherwise harmful content *created by users*
- **Moderation decisions:** Protecting, restricting, or refusing to host specific content types without losing immunity

### What Section 230 Does Not Protect

- **Federal criminal law:** § 230(e)(1) — no immunity from federal criminal prosecution
- **Intellectual property claims:** § 230(e)(2) — DMCA governs copyright; § 230 does not cover IP claims
- **FOSTA-SESTA (2018):** Federal and state civil sex trafficking claims; platforms can be liable if they "knowingly benefit" from sex trafficking
- **Platform's own content:** § 230 only protects third-party content; content the platform creates or substantially edits is not protected
- **Algorithmic amplification (evolving):** *Anderson v. TikTok* (3d Cir. 2024) held § 230 does not protect TikTok's algorithmic recommendation of harmful content to minors — the algorithm constitutes the platform's own "expressive activity"

### Section 230 and AI Products

The scope of § 230 for AI products is unsettled:
- Traditional chatbots (rule-based): generally considered third-party content presentation
- Generative AI (LLMs): the AI creates the response — it may be treated as the platform's own content, not third-party content, meaning § 230 may not apply
- AI-generated harmful outputs (e.g., chatbot output causing harm): likely not protected; courts have signaled this
- AI design choices: creating chatbots that produce harmful content = platform's own conduct; courts likely to find no § 230 protection

**Risk management:** Do not rely on § 230 as a primary defense for AI-generated content. Implement robust safeguards, content filtering, and accurate ToS disclaimers.
