# Technology Contracts Reference

## SaaS Agreement Architecture

A typical enterprise SaaS deal involves layered documents. Understand the hierarchy before drafting or reviewing:

1. **Master Service Agreement (MSA)** — governs the overall relationship; boilerplate that rarely changes deal-to-deal
2. **Order Form** — deal-specific terms: product, pricing, volume, committed term, renewal mechanics
3. **Statement of Work (SOW)** — implementation, professional services, customization scope (where applicable)
4. **Data Processing Agreement (DPA)** — mandatory under GDPR Art. 28 when provider processes EU personal data; increasingly standard globally
5. **Security Exhibit / Information Security Schedule** — technical security controls committed to by the provider
6. **Acceptable Use Policy (AUP)** — incorporated by reference; defines prohibited uses

**Key drafting principle:** The Order Form should control over the MSA on commercial terms. State explicitly: "In the event of conflict between this Order Form and the MSA, the Order Form controls." Failure to establish this hierarchy creates ambiguity courts resolve against the drafter.

---

## Critical MSA Clauses: What to Watch

### 1. Limitation of Liability (LoL)

**Standard provider position:** Total liability capped at 12 months of fees paid, excluding indirect/consequential damages (lost profits, lost data, loss of goodwill).

**What enterprises should push for:**
- Carve-outs from the cap for: data breaches involving customer data, IP indemnification, willful misconduct/gross negligence, death/personal injury, and confidentiality breaches
- Separate, higher cap (e.g., 2× or 3× annual fees) for data security incidents
- Deletion of "loss of data" from the consequential damages exclusion — data loss is a direct, foreseeable harm in SaaS

**Practical benchmark:** Industry standard is 12-month fee cap for general claims; security breaches should carry a higher cap (2× fees is defensible). Caps below 3 months of fees are aggressive provider positions that should be resisted.

**Statutory note:** LoL clauses are generally enforceable in B2B contracts (UCC § 2-719; Restatement (Second) of Contracts § 351), but courts will strike provisions that "fail of their essential purpose" or are unconscionable in consumer/non-negotiated contexts.

### 2. Indemnification

**Three-party structure:** Provider indemnifies customer for IP infringement claims by third parties (third-party IP indemnity). Customer indemnifies provider for misuse of the service or data the customer uploads.

**IP indemnification mechanics:**
- Provider pays defense costs and settlements for claims that the SaaS service infringes a third party's patent, copyright, trademark, or trade secret
- Provider's standard carve-outs: infringement caused by customer modifications, use with non-provider tools, or use after provider offered a non-infringing alternative
- Customer must give prompt written notice, grant sole control of defense and settlement, and cooperate reasonably

**Data breach indemnification:** Increasingly negotiated; customer-side counsel should push for provider to indemnify for breaches of provider's security obligations. Provider counsel limits this to direct damages only.

**Mutual indemnification:** Both parties should indemnify for breach of confidentiality, violation of law, and their own employees' torts.

### 3. Data Ownership

**The fundamental rule:** Customer data is the customer's. Draft it explicitly: "As between the parties, Customer retains all right, title, and interest in and to Customer Data." Do not leave this implicit.

**Derivative data / aggregate data carve-out:** Providers routinely include the right to use anonymized, aggregated data derived from customer data for product improvement and benchmarking. Acceptable if: (a) truly anonymized per GDPR/CCPA standards, (b) not re-identifiable, (c) not used to compete against the customer. Watch for broad language allowing use of "insights derived from Customer Data."

**Training data trap:** For AI/ML SaaS products, explicitly address whether customer data can be used to train AI models. If it can, this is a material disclosure requirement under GDPR (Art. 13/14), CCPA, and emerging AI transparency obligations. Best practice: default to no AI training on customer data; offer opt-in with clear description.

**Portability and export:** Customer should have the right to export all customer data in a standard format at any time and for 30–90 days post-termination. After that window, provider should certify deletion.

### 4. Service Level Agreements (SLAs)

**Standard SaaS SLA structure:**
- **Uptime commitment:** 99.9% (three nines) = ~8.7 hours downtime/year; 99.95% (three-and-a-half nines) = ~4.4 hours/year; 99.99% (four nines) = ~52 minutes/year. Mission-critical systems should demand 99.9% minimum.
- **Measurement window:** Monthly vs. annual. Monthly is more favorable to customers.
- **Exclusions:** Scheduled maintenance, customer-caused outages, force majeure, third-party provider failures (e.g., AWS/Azure/GCP). Audit these exclusions carefully — they can swallow the SLA.
- **Remedy:** Service credits (typically 10–30% of monthly fee per defined tier of downtime). Credits alone are inadequate for mission-critical systems.

**Negotiation targets:**
- Right to terminate without penalty if uptime falls below threshold for two or more consecutive months
- Credits automatically applied (no manual claim process)
- Financial credits that reflect actual business impact, not token 10%/month amounts
- Support response SLAs: P1 critical = <1 hour response, <4 hour resolution; P2 = <4 hours response, <24 hours resolution

**Provider manipulation to watch:** Many vendors define "availability" as partial functionality. Require definition to mean "all material functionality accessible and performing within stated response time thresholds."

### 5. Automatic Renewal / Evergreen Clauses

**Standard clause:** Contract renews automatically for successive 12-month terms unless either party provides written notice of non-renewal [60–90 days] before end of current term.

**Risks for enterprise customers:**
- Missed notice windows lock you into another full year at potentially unfavorable pricing
- Notice periods of 90+ days create procurement coordination challenges
- Price escalators (CPI + fixed %) are often embedded in renewal terms

**Best practice:** Negotiate notice window down to 30–60 days; add a price cap on automatic renewals (no more than CPI or 5% increase); require provider to send reminder 30 days before the opt-out window closes.

**Consumer context:** The FTC's updated "click-to-cancel" rule (16 CFR Part 425, finalized 2024) requires that consumers be able to cancel a subscription at least as easily as they signed up. Non-compliance: up to $51,744/day per violation.

### 6. Confidentiality

**Standard mutual NDA mechanics:** Each party treats the other's confidential information with at least the same degree of care as it treats its own confidential information (but no less than reasonable care); no disclosure to third parties without consent; use only for the purpose of the relationship.

**Key carve-outs from confidentiality:** Publicly available information, independently developed, received from a third party without restriction, required to be disclosed by law (with notice to disclose if legally permitted).

**Residuals clause (dangerous for disclosing party):** Many tech companies insert a "residuals" clause allowing employees to use confidential information they remember without deliberate memorization. Reject or heavily limit this clause — it can swallow trade secret protection.

**Duration:** Confidentiality obligations should survive termination for at least 3–5 years for general confidential information; indefinitely for trade secrets.

### 7. Governing Law and Dispute Resolution

**Governing law selection:** Vendors typically prefer Delaware (favorable corporate law, predictable courts) or their home state. Enterprises often push back to their own state. For international contracts, English law is a common neutral choice.

**Arbitration clauses:** JAMS or AAA commercial arbitration rules are standard; seat of arbitration should be specified. Arbitration provides confidentiality and limits runaway jury awards but increases cost for smaller disputes. Include a carve-out for injunctive relief and emergency measures.

**Class action waiver:** Standard in B2C terms; less common but appearing in B2B contexts. Post-Viking River Cruises, Inc. v. Moriana (2022) and subsequent developments, PAGA (California) claims remain partially contestable.

---

## API Terms of Service: Key Provisions

For companies offering or consuming APIs, these clauses require specific attention:

**Rate limits and usage quotas:** API ToS typically impose rate limits (requests per second/minute/hour) and monthly usage caps. Exceeding limits results in throttling or suspension. Enterprise agreements should specify guaranteed minimum throughput.

**Prohibited uses:** AI/LLM API ToS now commonly prohibit: training competing models on API output, scraping for datasets, circumventing safety filters, generating content for prohibited categories (e.g., CSAM, weapons of mass destruction). Violating these can result in immediate termination.

**Data retention by API provider:** Many API providers (especially AI APIs) retain inputs/outputs for safety monitoring. GDPR and HIPAA implications: do not send regulated personal data or PHI through APIs whose ToS permit this retention unless you have a DPA in place.

**IP in outputs:** AI API providers (OpenAI, Anthropic, Google) typically disclaim ownership of outputs and assign ownership to the customer, subject to usage policy compliance. Verify the specific assignment language in the applicable ToS.

**Indemnification for generated content:** API consumers are typically responsible for outputs they publish. Provider indemnification for third-party IP claims is narrow and conditioned on compliance with usage policies.

---

## Data Processing Agreements (DPAs)

A DPA is mandatory under GDPR Art. 28 whenever a controller engages a processor. Non-compliance is independently sanctionable under GDPR Art. 83(4) (up to €10M or 2% global turnover).

**Required DPA elements (GDPR Art. 28(3)):**
1. Process personal data only on documented instructions from the controller
2. Bind personnel to confidentiality
3. Implement appropriate technical and organizational security measures (Art. 32)
4. Assist the controller with data subject rights requests and security obligations
5. Delete or return all personal data upon termination (controller's choice)
6. Provide all information necessary for the controller to demonstrate compliance
7. Allow and contribute to audits

**Sub-processor provisions:** Processor must obtain controller's prior written authorization (general or specific) before engaging sub-processors. Processor must impose equivalent DPA obligations on sub-processors and remains liable for sub-processor failures.

**International transfer provisions:** If the processor transfers data outside the EEA, appropriate safeguards (SCCs, adequacy decision, BCRs) must be documented in the DPA. See `privacy.md` for transfer mechanism details.

**Breach notification obligation:** Processor must notify controller of a personal data breach without undue delay (industry practice: within 24–48 hours to allow controller to meet its 72-hour regulatory notification obligation). This processor notification timeline should be explicit in the DPA.

---

## Enterprise Software Licensing

**Perpetual vs. subscription:**
- **Perpetual license:** One-time fee for indefinite use rights to a specific version; maintenance and support fees typically 18–22% of license fee annually
- **Subscription (SaaS):** Recurring fee for access; all-in including updates, support, hosting; vendor retains more control; no license remains upon termination

**Named user vs. concurrent user vs. enterprise:**
- Named user: License tied to specific individuals (most restrictive; auditable)
- Concurrent user: Number of simultaneous users capped (more flexible for shift workers)
- Enterprise: Unlimited or all-employees; highest cost but operationally simplest

**Audit rights:** Most enterprise licenses include vendor audit rights (typically once per year, 30 days notice). Scope should be limited to license compliance; do not allow audit of source code, architecture, or business data beyond what is strictly necessary. Negotiate: (1) cap audit frequency, (2) require neutral third-party auditor under NDA, (3) cap underpayment remedy to true-up + no more than 10% penalty.

**Bundling and cross-selling traps:** Watch for clauses that automatically expand the license scope if you use the vendor's other products, or that convert a departmental license to an enterprise-wide license upon certain triggers.
