# Data Governance Reference

## Data Classification Framework

A tiered classification framework is the foundation of every defensible privacy and security program. Without classification, you cannot allocate security controls proportionally or demonstrate regulatory compliance.

### Standard Four-Tier Model

| Tier | Label | Description | Examples | Default Controls |
|---|---|---|---|---|
| 1 | **Public** | Intentionally shared with no expectation of confidentiality | Marketing materials, public documentation, press releases | Basic integrity; no confidentiality controls |
| 2 | **Internal / Non-Confidential** | Not public but no material harm from exposure | Internal wikis, org charts, general business processes | Access limited to employees; basic authentication |
| 3 | **Confidential** | Material harm from unauthorized disclosure | Customer lists, source code, financial forecasts, business strategies, non-public M&A | Encryption at rest and in transit; access controls; audit logs |
| 4 | **Restricted / Highly Sensitive** | Severe harm from unauthorized disclosure; regulatory obligation | Personal data (PII/PHI), API keys, cryptographic secrets, health data, financial account data | Encryption, MFA, strict need-to-know, DLP monitoring, regular access review |

### Regulated Data Sub-Classification

Within Tier 4, further sub-classify regulated data by regime to ensure correct handling:

| Sub-Type | Regulatory Regime | Special Requirements |
|---|---|---|
| **EU Personal Data** | GDPR | DPAs, lawful basis documentation, SCCs for transfers |
| **California PI** | CCPA/CPRA | Opt-out mechanisms, sensitive PI links |
| **PHI** | HIPAA | BAAs, minimum necessary, encryption |
| **PCI Data** (payment card data) | PCI DSS | Tokenization, network segmentation, quarterly scans |
| **Children's Data** | COPPA | Verifiable parental consent, restricted monetization |
| **Special Category Data** | GDPR Art. 9 | Explicit consent or Art. 9(2) condition |

---

## Data Retention Policies

A retention policy is both a legal requirement (GDPR Art. 5(1)(e) — storage limitation; CCPA § 1798.100) and a risk reduction tool — data you do not retain cannot be breached.

### Key Retention Principles

**Storage limitation (GDPR Art. 5(1)(e)):** Personal data must not be kept longer than necessary for the purpose for which it was collected. This is not aspirational — it is a compliance obligation with enforcement consequences.

**Purpose limitation (GDPR Art. 5(1)(b)):** Data collected for one purpose cannot be indefinitely retained "just in case" it might be useful later.

### Common Retention Schedules for Tech Companies

| Data Type | Typical Retention | Legal Driver |
|---|---|---|
| Customer account data (active) | Duration of account + 30 days post-deletion | Contractual; CCPA/GDPR |
| Customer account data (post-deletion request) | 30–90 days (technical deletion window) | GDPR Art. 17; CCPA § 1798.105 |
| Financial transaction records | 7 years | IRS; Sarbanes-Oxley (for public companies) |
| Employee records | Employment + 7 years | FLSA, ERISA, IRS |
| Security/audit logs | 90 days–2 years | HIPAA requires 6 years; PCI DSS requires 1 year; SOC 2 expects 90 days minimum |
| Incident response records | 5–7 years | Litigation hold; potential regulatory inquiry |
| Email communications (enterprise) | 3–7 years | Litigation hold requirements; FINRA/SEC for regulated entities |
| AI training data logs | At least 10 years (EU AI Act Art. 12 for high-risk) | EU AI Act |
| Backup tapes | Per classification of contained data | Retention must flow through to backups |

**Backup trap:** Many companies define retention policies for primary systems but forget backups. A deletion request must propagate to backups within a defined window (commonly 90 days for the next backup cycle). Document and automate this.

---

## Right to Deletion (Erasure) Implementation

### GDPR "Right to be Forgotten" (Art. 17)

The right to erasure applies when:
- Personal data is no longer necessary for the purpose it was collected
- Data subject withdraws consent (where consent was the lawful basis) and no other basis applies
- Data subject objects (Art. 21) and no overriding legitimate grounds exist
- Personal data has been unlawfully processed
- Legal obligation requires erasure

**Exceptions where erasure can be refused:**
- Exercising the right of freedom of expression and information
- Compliance with a legal obligation (tax records, court orders)
- Public health reasons
- Archiving purposes in the public interest, scientific or historical research, statistics
- Establishment, exercise, or defense of legal claims

**Implementation steps for tech companies:**
1. Map all data stores where personal data is held (database, backups, logs, analytics, third-party processors, cold storage)
2. Build a DSR intake workflow with identity verification
3. Implement deletion propagation to all data stores including sub-processors
4. Generate and retain deletion completion certificate (document what was deleted, where, when)
5. Notify third parties to whom you have disclosed the personal data (Art. 17(2))
6. Document exceptions applied (e.g., legal hold, backup propagation pending)

**Technical challenge:** Erasure from backup systems must be planned architecturally. Options: (a) delay deletion acknowledgment until backup window cycles, (b) use envelope encryption with key deletion, (c) maintain a "deletion list" that prevents restore of deleted records even from backup.

### CCPA Right to Delete (§ 1798.105)

Similar structure to GDPR but with differences:
- Business must delete upon verifiable consumer request
- Must direct service providers and contractors to delete the data
- Exceptions include: completing the transaction, security purposes, bug detection, short-term internal use, research, legal obligations, and other internal uses consistent with context of collection
- Timeframe: 45 days to respond (extendable once to 90 days total with notice)

---

## Consent Management

### Consent Under GDPR (Art. 7)

**Valid consent requirements:**
- **Freely given:** No power imbalance; no conditioning service on consent for unnecessary processing; granular (separate consent for separate purposes)
- **Specific:** Each purpose must be separately consented to; bundled consent is invalid
- **Informed:** Must know who is the controller, what data, for what purpose, right to withdraw
- **Unambiguous:** Clear affirmative action — pre-ticked boxes, silence, inactivity are invalid

**Record of consent:** Controllers must be able to demonstrate that consent was obtained (Art. 7(1)). Maintain a consent audit log: what consent was given, when, via what mechanism, for what purpose, and any withdrawal.

**Withdrawal:** Must be as easy as giving consent. One-click unsubscribe for marketing emails is the standard.

### Consent Management Platforms (CMPs)

For websites/apps with EU visitors, CMPs implement cookie consent banners. Key requirements:
- Reject-all option must be as prominent as accept-all
- No pre-ticked boxes or dark patterns (design that nudges users toward consent)
- Granular purpose-by-purpose consent
- IAB TCF v2.2 is the industry standard framework; ensure your CMP is certified
- Store consent records with timestamp, user pseudonym, purpose list, and CMP version

**CNIL (France), DPA (Austria), and ICO (UK) enforcement on cookie consent:** Most enforcement against major platforms has focused on: no reject-all option, consent banners using dark patterns, and failure to honor preferences. Fines range from €10K to €150M+.

---

## Data Breach Notification

### GDPR Breach Notification (Arts. 33–34)

**The 72-hour rule (Art. 33):**
- Controller must notify supervisory authority without undue delay and, where feasible, within 72 hours of *becoming aware* of a personal data breach (not when the breach occurred)
- "Awareness" = reasonable degree of certainty that a breach has occurred — a mere suspicion starts the clock
- Phased notification is permitted: notify within 72 hours with available information; supplement as investigation progresses
- Exception: no notification required if breach is unlikely to result in risk to individuals

**What the breach notification to supervisory authority must include (Art. 33(3)):**
1. Nature of the breach including categories and approximate number of individuals and records
2. Name and contact details of the DPO (or other contact)
3. Likely consequences of the breach
4. Measures taken or proposed to address the breach and mitigate its effects

**Notification to individuals (Art. 34):** Required when breach is likely to result in *high risk* to individuals. Must communicate in clear, plain language: nature of breach, contact details, likely consequences, measures taken. Without undue delay.

**Record-keeping:** Controllers must document all breaches regardless of whether notification is required (Art. 33(5)) — the breach register.

### US State Breach Notification Laws

All 50 states have breach notification laws. Key variations:

| State | Timeline | Threshold | Notes |
|---|---|---|---|
| California (CCPA/CPRA) | "Most expedient time possible"; no later than 30 days for consumers | 500+ CA residents: notify AG within 15 days | Expanded PI definition post-CPRA (2023) |
| New York (SHIELD Act) | "Most expedient time possible" without unreasonable delay | No numerical threshold | Must be "in the best interest of the affected persons" determination |
| Florida | 30 days | 500+ FL residents triggers AG notice | One of the shortest timelines |
| Colorado | 30 days to individuals; 30 days to AG if 500+ CO residents | | |
| Texas | 60 days | 250+ TX residents trigger AG notice | Texas SB 768 (2023) |
| Federal (HIPAA) | 60 days to individuals; 60 days to HHS; 500+ = media notification | PHI only | See privacy.md — HIPAA section |

**Multi-state enforcement:** 90% of US enforcement actions are multi-state coordination. Expect to notify multiple AGs simultaneously for any significant breach.

**Average US breach cost (2025):** $10.22 million (IBM Cost of a Data Breach Report 2025) — up 9% from prior year. This includes notification costs, credit monitoring, regulatory response, litigation, and reputational damage.

### Incident Response Planning

**The IR Plan must predate the breach.** Key components:

1. **Incident Response Policy:** Who is the IR team? What are escalation triggers? Who is the incident commander?
2. **Detection and identification procedures:** How will you know a breach has occurred? What are the indicators?
3. **Containment procedures:** Isolate affected systems; preserve forensic evidence; prevent ongoing access
4. **Notification decision tree:** When does legal/privacy get involved? What are the regulatory notification timelines? Who approves external notifications?
5. **External resources pre-contracted:** Forensic firm (IR retainer), outside counsel (attorney-client privilege for investigation), breach notification vendor, cyber PR firm
6. **Communication templates:** Pre-drafted notices to regulators, customers, employees, media
7. **Post-incident review:** Root cause analysis; remediation plan; lessons learned

**Attorney-client privilege for IR investigations:** Engage outside counsel before engaging the forensic firm; have the attorney retain the forensic firm. This creates privilege over investigation findings. Do not have the forensic firm report directly to the company.

**Forensic preservation:** Do not wipe or reimage affected systems before forensic imaging. Evidence of the breach and attacker TTPs is needed for regulatory responses and litigation defense.

---

## Data Governance Program: Implementation Checklist

**Foundation (Year 1 Priority):**
- [ ] Data inventory / data mapping: what data is collected, where stored, who accesses, what is shared with whom
- [ ] Data classification policy published and communicated
- [ ] Retention schedule established and automated where possible
- [ ] DPA executed with all vendors processing personal data
- [ ] Privacy notice updated to reflect actual practices
- [ ] DSR intake workflow operational

**Ongoing Operations:**
- [ ] Annual data mapping review
- [ ] Quarterly access review (remove stale permissions)
- [ ] Regular deletion job execution (retire data past retention period)
- [ ] Consent record audit
- [ ] Sub-processor list maintained and reviewed

**Breach Readiness:**
- [ ] Incident response plan documented and tested annually (tabletop exercise)
- [ ] IR retainer with forensic firm in place
- [ ] Outside counsel relationship established
- [ ] Cyber insurance policy reviewed for notification cost coverage
- [ ] Regulatory notification templates pre-drafted
- [ ] Breach notification decision matrix (what triggers which notification obligation)
