# Employment Law for Tech Companies Reference

## Independent Contractor vs. Employee Classification

Misclassification is among the highest-probability employment law risks for tech companies. The consequences include: back taxes (FICA, FUTA), back benefits (health, retirement, equity), wage claims, civil penalties, and in egregious cases, criminal referral.

### Federal Tests

**IRS Common Law Test / Right-to-Control Test (Rev. Rul. 87-41; IRS Topic 762):**

Three primary categories, each with sub-factors:

**1. Behavioral Control:**
- Does the company control how work is performed (not just the result)?
- Does the company provide tools, equipment, training?
- Does the company set work hours and location?

**2. Financial Control:**
- Does the worker have significant investment in their own tools/facilities?
- Can the worker profit or lose money on the engagement?
- Does the worker provide services to multiple clients?
- How is the worker paid (hourly vs. project fee)?

**3. Relationship Type:**
- Is there a written contract characterizing the relationship?
- Does the company provide employee benefits?
- Is the relationship permanent or for a specific project?
- Is the work integral to the company's core business?

**DOL Economic Reality Test (FLSA — effective January 2024, under revised 2024 rule; enforcement paused May 2025):**
The Biden administration's 2024 DOL rule reinstated a multi-factor "economic reality" test (totality of circumstances). However, the Trump DOL announced in May 2025 it would not enforce the 2024 rule and reverted to traditional economic reality principles. **Current DOL analysis uses pre-2024 factors**: degree of permanence, nature/degree of control, profit/loss opportunity, investments, integration into business, skill/initiative.

### State ABC Tests (More Employee-Friendly)

Several states use the ABC test, which presumes all workers are employees unless the hiring entity satisfies all three prongs:

**California ABC Test (AB 5, Lab. Code § 2750.3; Dynamex Operations West, Inc. v. Superior Court (2018)):**
- **A:** The worker is free from the control and direction of the hiring entity in connection with the performance of the work, both under the contract and in fact
- **B:** The worker performs work that is outside the usual course of the hiring entity's business
- **C:** The worker is customarily engaged in an independently established trade, occupation, or business of the same nature as the work performed

**Prong B is the killer:** If your core business is software development and you hire software developers as contractors, you fail Prong B. This is why Uber/Lyft/delivery companies faced massive ABC test exposure.

**AB 5 exemptions:** California has carved out numerous industries (gig platforms under Prop 22, licensed professions, certain creative workers, referral agencies, etc.). The exemption landscape is complex; consult counsel for specific classifications.

**Other ABC test states:** New Jersey, Massachusetts, Connecticut, Delaware, Illinois, Vermont. Each has variations — do not assume the California test applies identically elsewhere.

### Global Contractor Compliance

**EU Platform Workers Directive (2024):** Creates a legal presumption of employment for digital platform workers. Platforms must rebut this presumption or reclassify workers as employees. Effective for EU-based digital platforms.

**UK:** IR35 rules (off-payroll working) require end-client companies to assess whether contractors working through personal service companies (PSCs) would be employees but for the PSC structure. If yes, the end client must deduct income tax and NI.

**Brazil, India, Mexico:** All have formal employment unless genuine independence is established. India: contractor relationships require careful structuring to avoid PF/ESI obligations. Mexico: "subcontratación" reform (2021) significantly restricts outsourcing.

**Employer of Record (EOR) solutions:** For companies hiring internationally without a local entity, EOR platforms (Deel, Remote, Rippling) hire workers as employees of the EOR entity in the local country, providing legal compliance. Not a substitute for legal analysis — EOR shifts employer-of-record liability but contractual relationships with the EOR must be carefully structured.

---

## Non-Compete Enforceability

### California Ban

California Business & Professions Code § 16600: "Every contract by which anyone is restrained from engaging in a lawful profession, trade, or business of any kind is to that extent void."

- Non-compete agreements with California employees are void and unenforceable regardless of where the employer is headquartered or where the contract was signed
- SB 699 (2023) extended this: non-competes signed outside California are unenforceable against California employees; any attempt to enforce them is independently actionable
- Narrow exceptions: sale of business (§ 16601); dissolution of partnership (§ 16602); dissolution of LLC (§ 16602.5)

### FTC Non-Compete Rule — Current Status

- April 2024: FTC announced a rule banning most non-competes (16 CFR Part 910)
- August 2024: US District Court for the Northern District of Texas issued nationwide vacatur — rule is invalid and unenforceable
- September 2025: FTC voted 3-1 to dismiss its appeal and accede to the vacatur; the non-compete rule is dead federally
- **Current FTC posture:** Case-by-case enforcement under FTC Act § 5 for particularly egregious, overbroad non-competes (e.g., coercive use against low-wage workers)
- **Bottom line:** No federal non-compete ban. State law governs.

### State-by-State Overview (Key States for Tech)

| State | Enforceability | Key Conditions |
|---|---|---|
| California | Void (§ 16600) | No non-competes for employees; sale-of-business exception |
| Minnesota | Void (2023) | Non-competes entered after January 1, 2023 are void |
| North Dakota | Void | Very limited exceptions |
| Oklahoma | Void | |
| New York | Generally enforceable with limitations | Must be reasonable in scope, duration, geography; employer must provide consideration |
| Texas | Enforceable if ancillary to otherwise enforceable agreement | Must be reasonable in scope, duration, geography |
| Florida | Favorable to employers | Rebuttable presumption of enforceability; employee must prove unreasonableness |
| Illinois | Enforceable only for employees earning $75K+ | Must provide 14-day review period; adequate consideration beyond employment |
| Washington | Enforceable only for employees earning $116,593.18+/year (2024 threshold) | |

**Garden leave:** In lieu of or in addition to a non-compete, some companies (especially for senior/specialized employees) use garden leave (paid leave during the notice period) during which the employee is still employed and cannot work for a competitor. More defensible than post-employment restrictions.

### Trade Secret Alternative

Even where non-competes are unenforceable (California), employers retain strong protection via:
- **Defend Trade Secrets Act (DTSA), 18 U.S.C. § 1836:** Federal cause of action for trade secret misappropriation; allows injunctive relief, damages, and attorneys' fees
- **California Uniform Trade Secrets Act (Cal. Civ. Code § 3426):** State-level protection; employees cannot take with them or use a former employer's trade secrets
- A former employee who joins a competitor and uses confidential technical knowledge faces DTSA/UTSA exposure even in California

---

## Non-Disclosure Agreements (NDAs)

### Standard NDA Framework

**Mutual NDA (for partnership/M&A discussions):** Both parties share and receive confidential information; mutual obligations.

**Unilateral NDA (for product demos, job interviews):** Only one party shares; typically the company.

**Key clauses:**
- Definition of confidential information (broad but not so broad as to be unenforceable)
- Exclusions: publicly available, independently developed, received from third party without restriction, required disclosure by law
- Permitted uses: only to evaluate the disclosed purpose
- Restricted period: 2–5 years for general confidential information; indefinite for trade secrets
- Return/destruction of confidential information upon request or termination
- Injunctive relief clause: breach of NDA entitles disclosing party to seek injunctive relief without bond

### Post-#MeToo Restrictions on NDAs

**Federal — Speak Out Act (P.L. 117-224, Dec. 7, 2022):** Pre-dispute NDAs that prohibit disclosure of sexual harassment or sexual assault allegations are judicially unenforceable as to those claims. Applies only to predispute NDAs (NDAs signed *before* a dispute arises). Post-dispute settlement NDAs remain enforceable.

**State laws (California, New Jersey, Washington, Illinois, etc.):** Prohibit NDAs in settlement agreements that prevent disclosure of sexual harassment, assault, or discrimination claims. California SB 331 (2021): expanded prohibition to all workplace discrimination/harassment, not just sexual misconduct.

**UK Victims and Prisoners Act 2024 (effective Oct. 1, 2025):** Any NDA signed on or after October 1, 2025 is unenforceable insofar as it prevents a victim of crime from confiding in law enforcement, lawyers, or victim support services.

**Employment counsel takeaway for tech companies:**
- Never include NDA provisions preventing reporting to HR, internal compliance, regulatory authorities, or law enforcement
- Settlement NDAs for employee claims involving harassment/discrimination must be carefully structured to comply with applicable state law
- Include express carve-outs for whistleblower disclosures (protected by Dodd-Frank, SOX, NLRA § 7)

---

## Remote Work Multi-State Compliance

### Tax Nexus

Employing a worker in a state typically creates state income tax nexus for the employer. States' approaches vary:
- **Regular place of work:** Standard rule — employer must withhold state income tax for the state where the employee works
- **Convenience of employer rule (New York, Delaware, Nebraska, Pennsylvania, Arkansas):** If remote work is for the employee's convenience (not required by the employer), the income is taxed by the employer's state as if the employee worked in the office

**New York's aggressive approach:** Even if an employee lives in Connecticut and works from home, if remote work is not required by the NY employer's business necessity, NY income tax applies to NY-sourced income. This creates double taxation issues.

### State-Specific Employment Law Obligations

Each state where an employee works applies its employment laws:

| Obligation | Variation Example |
|---|---|
| Minimum wage | Ranges from federal $7.25 to $17.50+/hour in Seattle |
| Overtime | California requires daily overtime (>8 hours/day) vs. federal weekly (>40 hours/week) |
| Mandatory breaks | California: 10-minute paid rest break per 4-hour work period; 30-minute unpaid meal break if >5 hours |
| Sick leave | Most states/cities require paid sick leave; amounts and accrual rates vary |
| FMLA equivalents | California CFRA, New York FMLA, etc. often more expansive than federal FMLA |
| Pay transparency | Colorado, California, New York, Washington require salary range disclosure in job postings |
| Non-compete | See above |
| Background checks | "Ban the box" laws in many states/cities restrict pre-offer criminal history inquiries |

### Global Remote Work

Workers physically located in another country, even if employed by a US entity, typically trigger:
- **Local labor law application:** The host country's employment protections apply regardless of the employment agreement's governing law
- **Permanent establishment (PE) risk:** An employee working from a foreign country may create a corporate tax PE for the US company in that country
- **Work authorization:** Workers must have the right to work in the country where they perform services
- **Data privacy:** GDPR applies to EU-based workers even if employed by US company; employer data processing triggers GDPR Art. 88 employee data rules

**Employer of Record (EOR) vs. Foreign Entity:**
- EOR: faster, compliant for small number of workers; EOR becomes the legal employer; company pays EOR service fee; limited control over employment terms
- Foreign subsidiary/entity: better for larger teams; full control; takes 3–6 months and meaningful cost to establish; requires local payroll, benefits, HR compliance
