# AI IP Scenarios — Quick Decision Trees

## Scenario A: "Who owns the AI model we trained?"

**Decision tree:**

1. Was the model trained entirely by employees within scope of employment?
   - YES → Company owns it (work-for-hire for copyright; patent rights depend on assignment agreements)
   - NO → Continue to 2

2. Did any contractors or consultants contribute to training code, dataset curation, or model architecture?
   - YES → Check if they signed IP assignment agreements
   - NO assignment agreement → **Critical risk**: they may own their contributions
   - Assignment signed → Verify it covers "model weights, training procedures, and all outputs"

3. Was the model trained on a cloud provider's infrastructure (AWS, GCP, Azure)?
   - Cloud provider ToS do NOT claim ownership of your model — this is a common misconception
   - Check ToS for any unusual data use provisions on inputs, but outputs are yours

4. Did any contributor use a prior employer's confidential information in training data or architecture?
   - HIGH RISK — potential trade secret misappropriation claims from prior employer
   - Conduct due diligence before fundraising

**Protection stack for model weights:**
- Trade secret (primary): Access controls, encryption, NDAs, documented security measures
- Copyright: Architecture/training code (not weights themselves)
- Patent: Novel training algorithms if Alice-eligible (see Domain 1)

---

## Scenario B: "Can we use [data source] to train our model?"

**Data source risk matrix:**

| Source | License | Risk | Action |
|---|---|---|---|
| Common Crawl | CC-BY-4.0 for curated sets; raw crawl unclear | Medium | Document provenance; implement memorization guards |
| GitHub public repos | Varied (MIT, Apache, GPL, no license) | Medium | Check Copilot litigation developments; respect AGPL |
| Wikipedia | CC BY-SA 3.0 | Low | Attribution may be required in model documentation |
| Reddit, Twitter/X scrapes | ToS restrictions; underlying content copyrighted | High | Obtain API license; ToS compliance required |
| News articles (AP, Reuters, NYT) | Licensing market exists | High | Obtain data license; fair use defense weakened by market existence |
| Books (Project Gutenberg) | Public domain for pre-1928 works | Low | Verify publication date |
| Books (pirated — LibGen, etc.) | Infringement | Critical | Do not use; *Bartz v. Anthropic* confirms violation |
| Licensed training datasets (Hugging Face, licensed) | Depends on specific license | Low-Medium | Verify ML training use is explicitly permitted |
| User-submitted content | Your ToS governs | Low (with proper ToS) | Ensure ToS includes training use grant |
| Synthetic data | No copyright in AI-generated content | Low | Watch for upstream contamination |

---

## Scenario C: "Can we open-source our model?"

**Pre-open-source checklist:**

1. **Training data audit:** Does any training data carry a license that restricts model distribution? (Rare, but some dataset licenses restrict commercial use of derived models.)

2. **Dependency licenses:** What licenses govern your training framework (PyTorch → BSD; TensorFlow → Apache 2.0; Hugging Face Transformers → Apache 2.0)? Apache 2.0 is safe to incorporate.

3. **Export control review:** Large model weights may trigger U.S. export control regulations (EAR). Models with potential military/dual-use applications or trained on controlled data may require BIS review before public release.

4. **Trade secret election:** Once you open-source model weights, you cannot reclaim trade secret protection for those weights. Make this decision deliberately.

5. **Choose the right license for your model:**
   - Apache 2.0: Maximum adoption, patent grant included
   - AGPL v3: Forces share-alike on derivative models deployed as services (use if you want to prevent competitors from serving your model commercially without contributing back)
   - Llama 2/3 style custom license: Restricts uses above certain user thresholds — technically source-available, not open source; controversial in AI community
   - CC BY 4.0 (for model weights/documentation separately from code): Common for research releases

6. **Model Card and system card:** Not legally required but increasingly expected; documents capabilities, limitations, and intended use.

---

## Scenario D: "Our competitor may be using our trade secrets"

**Immediate steps:**

1. **Preserve evidence:** Preserve all documentation showing what information was kept secret and how it was protected. Do not destroy anything.

2. **Document the misappropriation theory:** What specific information? Who had access? When did the competitor's product appear with similar characteristics?

3. **DTSA emergency relief:** DTSA allows ex parte seizure orders in extraordinary circumstances (18 U.S.C. § 1836(b)(2)) — rare, but available where immediate irreparable harm and inadequate standard injunctive relief remedy. Standard TRO/preliminary injunction is more common.

4. **Evidence of "reasonable measures":** Prepare to show your access logs, NDA list, security policies, and training records — these become exhibits.

5. **Former employee analysis:** If a former employee is now at the competitor, review their last 90 days of system access, email patterns, and what they downloaded before departure.

6. **Engage IP litigation counsel immediately** — DTSA has a 3-year statute of limitations from date of actual or constructive discovery (18 U.S.C. § 1836(d)).

---

## Scenario E: "We received an NPE demand letter"

**Initial response protocol (first 72 hours):**

1. **Do not ignore** — failure to respond can constitute willful infringement after notice
2. **Preserve all documents** related to the accused product/feature (litigation hold)
3. **Do not make admissions** in any response
4. **Obtain a copy of the asserted patent** from USPTO (free via patents.google.com)
5. **Preliminary invalidity assessment:** Check priority date, claims construction, prior art that predates filing
6. **Assess PTAB IPR option:** Can you file an IPR challenging validity? Must be filed within 1 year of service of a complaint (35 U.S.C. § 315(b))

**The economics:**
- Typical NPE license demand for small company: $50,000-$500,000
- Typical NPE litigation cost if you fight: $2-$6 million through trial
- IPR cost: $200,000-$500,000 with 60-70% success rate
- Settlement leverage: Having IPR as credible threat often induces favorable settlement terms

**Engage IP litigation counsel** (patent litigator, not prosecution attorney) within 72 hours of receipt.
