# Corporate Governance Reference

## Board Fiduciary Duties

### Duty of Care

Directors must act with the care that a person in a like position would reasonably believe appropriate under similar circumstances. Under Delaware law (8 Del. C. § 141(e)), a director fulfills the duty of care by:
- Being informed before making decisions
- Reading materials provided in advance of meetings
- Asking questions and seeking expert advice when warranted
- Attending board meetings (persistent absenteeism without cause can constitute breach)
- Reviewing financial statements and audit findings

**Business Judgment Rule:** Courts presume directors acted on an informed basis, in good faith, and in the honest belief that the action was in the best interest of the company. To rebut the presumption, plaintiff must show: director had a conflict of interest (triggering entire fairness review), failed to inform themselves, or acted in bad faith.

**Gross negligence standard:** Under Delaware law, only gross negligence — recklessness that falls far below what a reasonable person would do — rebuts the business judgment rule. This is a high bar; most director-facing lawsuits fail absent a conflict of interest.

**DGCL § 102(b)(7) exculpation:** Delaware corporations can include in their certificate of incorporation a provision eliminating personal monetary liability of directors for duty-of-care breaches (but not duty of loyalty, bad faith, intentional misconduct, unlawful dividends, or improper personal benefit). Most Delaware corporations include this provision.

### Duty of Loyalty

Directors must put the corporation's interests above their own. Key manifestations:

**Conflict of interest transactions:** When a director has a personal financial interest in a transaction (e.g., the company contracts with a business the director owns), the transaction is subject to heightened scrutiny. Under DGCL § 144, a conflicted transaction is not void if:
1. Approved by a majority of disinterested directors after full disclosure of material facts, OR
2. Approved by a majority of disinterested shareholders after full disclosure, OR
3. The transaction is fair to the corporation

**Best practice:** Board minutes should document the disclosure, the disinterested director vote, and the fairness determination. Do not allow conflicted directors to participate in the vote.

**Corporate opportunity doctrine:** Directors cannot seize business opportunities that belong to the corporation without first offering them to the corporation and receiving rejection. A director cannot use corporate information, resources, or position to take a business opportunity in the corporation's line of business or that the corporation has an interest in.

**Executive compensation:** Setting compensation of executive-directors requires care. Delaware courts apply enhanced scrutiny (entire fairness review in extreme cases) to compensation that appears entirely unfair to the corporation. Compensation committees of independent directors are standard governance.

### Duty of Obedience

Primarily a nonprofit doctrine, but applies to for-profits in terms of acting within the company's legal authority — directors must not cause the company to act ultra vires (beyond its corporate powers), violate its charter, or violate applicable law.

### Cybersecurity and AI as Board Fiduciary Issues

**Post-SolarWinds, Colonial Pipeline, and SEC rules:** Courts and regulators increasingly expect boards to engage with cybersecurity as a governance matter. SEC Rules (effective Dec. 2023) require public companies to disclose material cybersecurity incidents within 4 business days (Form 8-K) and annually disclose board cybersecurity oversight and risk management processes (Form 10-K).

**Board-level AI governance:** Delaware courts have not yet issued AI-specific fiduciary guidance, but the business judgment rule analysis applies: directors who ignore material AI-related risks (regulatory, safety, reputational) without informed analysis may face enhanced scrutiny if harm results. Best practice: full board or audit committee should receive regular AI risk briefings.

---

## Consent Mechanics: Written Consents vs. Board Meetings

### Delaware Board Action

**Board meetings:** Typically require: (1) proper notice (unless waived), (2) quorum (usually majority of directors), (3) majority vote of directors present.

**Written consents (DGCL § 141(f)):** In lieu of a meeting, the board may act by unanimous written consent of all directors. Must be signed by all directors (not just a majority — unanimous is required for board written consents). Effective upon last signature unless consent specifies later effective date. Must be filed in the corporation's minute book.

**Stockholder written consents (DGCL § 228):** Stockholder action can be taken without a meeting if consents of the holders of the minimum number of votes necessary to authorize the action are obtained. Used for Series A closings, option plan approvals, and charter amendments to avoid delay of a stockholder meeting.

**When consents are appropriate:**
- Routine board actions (option grants, officer designations, opening bank accounts, approving contracts) — written consents are standard; minimize meeting overhead
- Material transactions (M&A, fundraising, major strategic pivots) — should be discussed at actual meetings with deliberation documented; pure written consents create governance record vulnerability

**Risk of lazy consent practice:** Courts applying entire fairness review in conflict transactions want to see evidence of deliberation, not just a rubber-stamp signature on a consent. For any conflicted or significant transaction, hold an actual meeting (or at least a telephonic meeting) and document discussion in minutes.

---

## Venture Capital Governance Rights

### Preferred Stock Rights Stack (Series A Typical)

**Economic rights:**
- **Liquidation preference:** 1× non-participating liquidation preference is the current market standard (Cooley, NVCA Model). Participating preferred (where investors get liquidation preference AND share in remaining proceeds) is atypical at Series A except in down markets or with inexperienced founders.
- **Anti-dilution:** Weighted-average anti-dilution protection (broad-based preferred) is standard. Ratchet/full-ratchet anti-dilution is highly aggressive and founder-unfriendly; resist unless in a distressed situation.
- **Dividend rights:** Typically 8% cumulative dividends (but rarely declared or paid; mostly relevant in M&A waterfall calculations)
- **Conversion:** Preferred automatically converts to common on IPO (threshold: usually 50% of outstanding preferred votes; minimum IPO price 3–4× the original purchase price)

**Control rights:**
- **Protective provisions (voting rights):** Preferred stockholders (typically as a class) must approve certain major actions: issuing new preferred or senior preferred, amending charter or bylaws in ways adverse to preferred, authorizing M&A or asset sale, incurring debt above a threshold, changing board size
- **Board composition:** Series A lead typically receives one preferred director seat; founders typically control 2+ common director seats; one independent director (may be observer initially)
- **Information rights:** Right to receive audited annual financials within 120 days; unaudited quarterly financials within 45 days. Major Investors (above ownership threshold, e.g., 5%) may receive monthly financials.

**Transfer-related rights:**
- **Right of First Refusal (ROFR):** Company (and investors on a pro-rata basis) have the right to purchase shares before a stockholder can sell to a third party
- **Co-sale rights (tag-along):** Investors can participate in a founder's sale on the same terms; prevents founders from selling and leaving investors behind
- **Drag-along:** Defined majority can compel all stockholders to approve and participate in an M&A transaction; prevents holdouts from blocking exits

**Future round rights:**
- **Pro-rata (pre-emptive) rights:** Right to participate in future financing rounds to maintain ownership percentage. Major investors typically get pro-rata rights; minor investors may not. 78% of VC deals include pro-rata rights (Sifted). Negotiate carefully — broad pro-rata rights can complicate future financing syndication.
- **Pay-to-play provisions:** If an investor fails to exercise their pro-rata in a down round, their preferred converts to common. This protects founders and new investors from non-participating diluted preferred holders.

**Information rights:**
- 49% of convertible financings in 2024 included investor information rights (26% increase from 2023)
- Information rights are typically limited to Major Investors (e.g., $500K+ invested)
- Include confidentiality obligation on investors receiving non-public financial information (standard in NVCA Model documents)

### NVCA Model Documents

The National Venture Capital Association maintains standardized model documents:
- Series A Term Sheet
- Restated Certificate of Incorporation (Certificate of Incorporation with Series A preferred stock terms)
- Investors' Rights Agreement (registration rights, information rights, right of first offer)
- Voting Agreement (drag-along; board composition)
- Right of First Refusal and Co-Sale Agreement

Using NVCA models reduces negotiation cost and signals to investors that founders are sophisticated. Material deviations should be flagged and understood before signing.

### SAFEs and Convertible Notes (Pre-Seed)

**Simple Agreement for Future Equity (SAFE):** Y Combinator standard; converts to equity at next priced round. Post-money SAFE (current standard): valuation cap is on post-money basis (including all SAFEs converting). Founders must understand dilution math — multiple SAFEs at high valuations can create significant dilution at Series A.

**Convertible Note:** Debt instrument with interest; converts at Series A. Maturity date creates pressure (must repay or extend if no financing). Discount (typically 20%) and valuation cap are key economic terms. Most pre-seed is now done via SAFEs (no maturity date, no interest, simpler).

---

## Conflicts of Interest: Practical Management

**Director disclosure obligation:** Any director who has a material financial interest in a transaction before the board must: (1) disclose the conflict in writing before the board discusses the transaction, (2) recuse from discussion and vote, (3) ensure disclosure is documented in board minutes.

**Common tech startup conflicts:**
- CEO/founder is also a significant stockholder — approving their own compensation
- Board member is a partner at an investor fund — approving transactions that benefit the fund
- Board member is also a customer or supplier — approving contracts with their business
- Board member serves on multiple portfolio company boards — corporate opportunity issues

**Audit committee:** Once a company has outside investors or approaches Series B, establishing an audit committee of independent directors to review related-party transactions, approve auditors, and oversee financial reporting is best practice and required for public companies.

**409A valuations:** For private companies granting stock options, regular 409A appraisals (typically by an independent third party) establish the fair market value of common stock. Options granted at FMV are not subject to IRC § 409A income tax acceleration. Failure to get or rely on a 409A valuation creates option holder tax risk and potential board liability for approving options at below-FMV prices.
