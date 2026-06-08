# Cybersecurity Law Reference

## Computer Fraud and Abuse Act (CFAA)

**Applicable law:** 18 U.S.C. § 1030 (Computer Fraud and Abuse Act, 1986, as amended).

The CFAA is the primary US federal cybercrime statute. It criminalizes "unauthorized access" to protected computers (virtually any computer connected to the internet).

### Key CFAA Prohibitions

| Provision | Conduct | Penalty |
|---|---|---|
| § 1030(a)(1) | Unauthorized access to obtain classified government information | Up to 10 years |
| § 1030(a)(2) | Unauthorized access to obtain financial records, communications, or any computer information | Up to 5 years (10 if second offense) |
| § 1030(a)(3) | Unauthorized access to government computers | Up to 10 years |
| § 1030(a)(4) | Unauthorized access with intent to defraud (gaining "something of value") | Up to 5 years (10 if second offense) |
| § 1030(a)(5)(A) | Knowingly causing transmission of code/command to damage protected computer | Up to 10 years |
| § 1030(a)(5)(B)/(C) | Reckless/negligent damage to protected computer | Up to 5 years |
| § 1030(a)(7) | Extortion threats involving damage to computers | Up to 10 years |

**Civil private right of action (§ 1030(g)):** Any person who suffers damage or loss may bring a civil action. Minimum threshold: $5,000 in losses over 12 months, OR damage to medical systems, physical injury, threat to public health/safety, or government computer damage.

### "Unauthorized Access" After Van Buren

*Van Buren v. United States* (2021): The Supreme Court held that a person "exceeds authorized access" under § 1030(a)(2) only when accessing *areas* of the computer they are not permitted to access — not by misusing data they are authorized to access for an improper purpose. This narrowed CFAA significantly:
- Employees who access company databases for personal purposes, while prohibited by policy, do not violate the CFAA if they are authorized to access those databases in the course of their work
- Scraping publicly accessible websites does not violate CFAA (*hiQ Labs v. LinkedIn*, 9th Cir. 2022 — affirmed after *Van Buren* remand)

**Practical takeaways:**
- Access controls must be implemented at the system/directory/database level (not just policy-level) to create meaningful CFAA exposure for unauthorized users
- Insider threat cases: employees misusing systems they are authorized to access face CFAA exposure only if they access systems/areas beyond their authorization, not merely for improper purposes
- External threat/hacking: still clearly covered — unauthorized access to systems the attacker has no permission to access

---

## State Cybersecurity Laws

### New York SHIELD Act (Stop Hacks and Improve Electronic Data Security Act)

**Effective:** March 21, 2020. **Applies to:** Any person or business that owns or licenses computerized data that includes "private information" of a New York resident, regardless of where the business is located.

**Two core obligations:**

**1. Reasonable Security Requirement:**
Any entity maintaining private information of NY residents must implement and maintain "reasonable" administrative, technical, and physical safeguards. Reasonable safeguards include:
- Designating employee(s) responsible for security program
- Identifying reasonably foreseeable internal/external risks; assessing sufficiency of existing safeguards
- Training employees in security practices
- Selecting service providers capable of maintaining safeguards (contract protections)
- Adjusting security program in light of developments

**Safe harbor:** Companies in compliance with HIPAA Security Rule, GLBA Safeguards Rule, NY DFS Cybersecurity Regulation (23 NYCRR 500), or the NIST Cybersecurity Framework satisfy the reasonable safeguards requirement.

**2. Breach Notification:**
Any entity that experiences a breach of NY private information must notify affected NY residents expeditiously. Notification to NY Attorney General required (no threshold). The breach must be the more-likely-than-not cause of harm to trigger notification (not just a mere possibility).

**Penalties:**
- Failure to notify: up to $20/failed notification; max $250,000
- Failure to maintain reasonable safeguards: up to $5,000 per violation

**"Private information" definition (NY SHIELD):** Name + (SSN, driver's license number, account number + access code, biometric information, username/email + password/security Q&A). Expanded in 2022 to include: credit/debit card number alone if CVV + expiration is also compromised.

### California SB 1386 (2002) and AB 1950 / SB 327 (IoT)

**SB 1386 (now Cal. Civil Code §§ 1798.29, 1798.82):** California's original 2002 breach notification law — the first in the US. Still in effect; superseded for comprehensive privacy by CCPA/CPRA.

**SB 327 / AB 1886 (IoT Cybersecurity):** Devices that connect to the internet must have "reasonable security features" appropriate to the nature of the device and information it may collect/contain. Effective January 1, 2020.

### New York DFS Cybersecurity Regulation (23 NYCRR Part 500)

Applies to entities regulated by the NY Department of Financial Services (banks, insurance companies, licensed financial service providers). Not broadly applicable to all tech companies, but any fintech company licensed in NY must comply.

Key requirements: written cybersecurity policy, CISO, access controls, multi-factor authentication, encryption, vulnerability assessment, penetration testing, incident response plan, annual senior officer certification, 72-hour breach notification to DFS.

---

## SOC 2 and Its Legal Significance

**SOC 2** (Service Organization Control 2) is an auditing framework developed by the AICPA based on Trust Services Criteria (TSC): Security, Availability, Processing Integrity, Confidentiality, and Privacy.

**Types:**
- **SOC 2 Type I:** Point-in-time assessment of whether security controls are suitably designed
- **SOC 2 Type II:** Assessment over a period (typically 6–12 months) that controls are both suitably designed AND operating effectively. Type II is the standard for enterprise procurement.

### Legal Significance of SOC 2

**Contractual leverage:** Enterprise customers routinely require SOC 2 Type II in MSAs and vendor security exhibits. Inability to produce SOC 2 Type II can block enterprise sales.

**Liability shield:** A current SOC 2 Type II report demonstrating reasonable security controls is evidence in litigation that the company implemented a reasonable security program. Courts and regulators (including FTC in § 5 enforcement) look favorably on companies with recognized third-party security certifications.

**SHIELD Act safe harbor:** SOC 2 compliance with the Security Trust Service Criterion is generally sufficient to satisfy NY SHIELD Act's "reasonable safeguards" requirement (courts have not directly addressed but the statutory safe harbor for NIST CSF is analogous).

**Insurance:** Cyber insurers routinely require evidence of SOC 2 compliance (or similar controls) as a condition of coverage. MFA implementation is now a universal cyber insurance requirement.

**Limitation:** SOC 2 is not a legal certification and does not guarantee regulatory compliance. A SOC 2 report covers the scope and time period assessed; it does not cover your entire data environment. A breach of a system not in scope for SOC 2 does not benefit from the SOC 2 halo.

### SOC 2 vs. ISO 27001

| Dimension | SOC 2 | ISO 27001 |
|---|---|---|
| Governance body | AICPA (US) | ISO/IEC (international) |
| Primary market | US (enterprise B2B SaaS) | International; EU procurement |
| Output | Report (not certification) | Certificate |
| Scope | Trust Service Criteria | 114 controls across 14 domains (Annex A) |
| Frequency | Annual renewal | 3-year cert cycle with annual surveillance audits |
| Best for | US enterprise sales | EU/international enterprise sales; some industries require it |

---

## Cyber Insurance

### Coverage Overview

**First-party coverage (your own costs):**
- Breach investigation and forensics costs
- Legal expenses (regulatory response, breach notification legal advice)
- Notification costs (printing, postage, credit monitoring)
- Public relations/crisis communications
- Business interruption losses (downtime during incident)
- Extortion/ransomware payment (controversial; some policies cover, some exclude)
- Data recovery and system restoration costs

**Third-party liability coverage:**
- Claims from customers, partners, and other third parties whose data was compromised
- Regulatory fines and penalties (coverage varies widely; GDPR fines often excluded or sublimited)
- Privacy liability
- Media liability (defamation, copyright infringement)
- Network security liability

### Key Exclusions to Watch

- **War/nation-state exclusion:** Major insurers began adding/clarifying war exclusions after NotPetya (attributed to Russian state actors). *Merck v. Ace Insurance* (NJ Superior Court, 2023): court found in favor of Merck, rejecting war exclusion for cyber attacks not directed at Merck specifically. Review exclusion language carefully.
- **Prior acts / known losses:** If a breach occurred before the policy period began, typically excluded
- **Inadequate security:** If a breach results from failure to implement basic security controls required by the policy, insurer may deny coverage
- **Social engineering sublimits:** Phishing/BEC (Business Email Compromise) losses often sublimited to $100K–$500K even on policies with $5M+ limits

### Cyber Insurance Market Trends (2024–2025)

- Premiums increased 20–50% annually from 2020–2023; market stabilizing in 2024–2025
- MFA is a universal underwriting requirement; lack of MFA = coverage denial or high premium
- Insurers now require evidence of EDR/XDR deployment, backup testing, and vendor risk management programs
- Ransomware coverage: many insurers now exclude ransomware payment coverage entirely, or require proof of no known vulnerabilities before ransomware event

---

## Breach Liability Framework

### Elements of Negligence for Data Breaches

Plaintiffs bringing breach claims typically assert: (1) duty to protect data, (2) breach of that duty (inadequate security), (3) causation (breach led to harm), (4) damages (identity theft, fraud losses, time/inconvenience).

**Standing challenge:** Many US circuit courts require plaintiffs to demonstrate actual injury (not just risk of future harm) to maintain standing. *Clapper v. Amnesty Int'l* (2013); but *TransUnion LLC v. Ramirez* (2021) held that risk of future harm is insufficient for Article III standing for damages claims. Circuit split on what constitutes sufficient harm (fraudulent charges vs. credit monitoring costs vs. spent time resolving issues).

**Class certification:** Breach cases face class certification challenges because damages are highly individualized. Courts increasingly certify classes for injunctive relief while denying damages class certification.

### Negligence Per Se

If a company violates a cybersecurity statute (HIPAA, GLBA, SHIELD Act), the violation can constitute negligence per se — plaintiff need not prove that the company failed to meet a reasonable standard of care, because the statute sets that standard. This is a significant aggravating factor in breach litigation.

### Settlement Trends

- Average US data breach class action settlement: $2M–$10M for consumer breaches; larger for healthcare/financial sector
- Regulatory settlements (FTC, state AG, HHS OCR) separate from civil litigation
- Companies with SOC 2 Type II and documented IR plans typically settle faster and for less

---

## Cybersecurity Compliance Program: Priority Checklist

**Legal minimums (assume applicable to any SaaS handling personal data):**
- [ ] Written information security policy (WISP) — required by several state laws
- [ ] Annual risk assessment
- [ ] Incident response plan with defined notification responsibilities
- [ ] Data breach notification templates (regulatory, customer, media) pre-drafted
- [ ] Vendor/third-party risk management program — require SOC 2 from vendors processing your data
- [ ] Employee security training (annual minimum; phishing simulation recommended)
- [ ] Access controls: role-based access, principle of least privilege, quarterly access review
- [ ] MFA on all administrative accounts, remote access, and systems storing sensitive data
- [ ] Encryption: data at rest (AES-256) and in transit (TLS 1.2+)
- [ ] Log retention: minimum 90 days accessible; 12 months archived
- [ ] Penetration testing: annually at minimum; after major infrastructure changes

**For companies processing significant personal data:**
- [ ] SOC 2 Type II audit (engage auditor; scope and implement controls)
- [ ] Cyber insurance policy (first-party and third-party liability)
- [ ] DPIA for high-risk data processing activities
- [ ] Bug bounty program or responsible disclosure policy
