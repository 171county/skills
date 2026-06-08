---
name: business-finance-expert
description: Expert-level business finance advisor covering corporate finance fundamentals (TVM, NPV, IRR, WACC, capital structure), financial statement analysis, budgeting and forecasting, management accounting, tax strategy for tech companies (including 2025 OBBBA Section 174 restoration, QSBS expansion), treasury management, debt financing (venture debt $68.8B market 2025), M&A fundamentals, IPO readiness, FinOps and cloud cost optimization (public cloud $723.4B in 2025), SaaS revenue accounting (ASC 606), equity management, investor relations, financial risk management, and CFO strategic partnering. Expert-level reference calibrated through mid-2026.
---

# Business Finance Expert

## Overview and Expert Mandate

Finance is strategy made quantitative. Technology companies that understand their unit economics, capital allocation logic, and cash dynamics outcompete those that treat finance as a reporting function. This skill provides expert-level financial guidance for growing tech companies — from first principles through IPO readiness — covering every major financial domain with precision calibrated to the 2025-2026 environment.

---

## 1. Corporate Finance Fundamentals

### Time Value of Money (TVM)

The foundational principle: a dollar today is worth more than a dollar tomorrow because it can earn a return. All corporate valuation flows from this axiom.

**Core TVM mechanics:**
- **Future Value:** `FV = PV × (1 + r)^n`
- **Present Value:** `PV = FV / (1 + r)^n`
- **Annuity PV:** `PV = PMT × [1 − (1+r)^−n] / r` — used in subscription revenue modeling
- **Perpetuity:** `PV = C / r` — used in terminal value calculations (Gordon Growth Model)

**Tech company applications:**
- Discounting future ARR streams to assess LTV
- Evaluating whether CAC investment is justified by discounted future subscription cash flows
- Valuing deferred revenue obligations in M&A

### Net Present Value (NPV)

`NPV = Σ [CFt / (1+r)^t] − Initial Investment`

Positive NPV = investment creates value above the hurdle rate. **The single most important capital allocation metric.**

**Decision rule:** Accept all projects with NPV > 0. Between mutually exclusive projects, choose highest NPV.

**Common errors in tech:**
- Using WACC as discount rate for all projects regardless of risk profile — high-risk AI/R&D initiatives need higher rates
- Not modeling terminal value — typically 60-80% of total NPV in high-growth SaaS valuations
- Ignoring option value: R&D projects have embedded real options (expand, delay, abandon)

### Internal Rate of Return (IRR)

IRR = discount rate at which NPV = 0. Annualized expected return from an investment.

**Decision rule:** Accept where IRR > WACC (hurdle rate).

**IRR pitfalls:**
- **Multiple IRRs:** Cash flow sign reversals create multiple mathematical solutions
- **Reinvestment assumption:** IRR assumes cash flows reinvested at the IRR rate — often unrealistic
- **Scale ignorance:** $1M project at 40% IRR < $100M project at 15% IRR; always compare NPVs

**Modified IRR (MIRR):** Addresses reinvestment assumption; preferred for internal capital allocation.

### Weighted Average Cost of Capital (WACC)

WACC = blended required return for all capital providers. The hurdle rate for capital allocation and DCF discount rate.

```
WACC = (E/V) × Re + (D/V) × Rd × (1 − T)
```

**Cost of Equity (CAPM):**
```
Re = Rf + β × (Rm − Rf)
```
- Rf = Risk-free rate (10-year Treasury ~4.2% mid-2025)
- β = Levered beta (relative market movement)
- (Rm − Rf) = Equity risk premium (~5.0-5.5% per Damodaran 2025)

**Industry benchmarks (2025):**
- Software/SaaS WACC: 9-11%
- High-growth pre-revenue AI: 12-15% (higher beta, no debt tax shield)
- Large-cap tech: 7-9%

**Common WACC mistakes:**
- Using book value instead of market values
- Using historical beta without adjusting for leverage changes
- Single WACC company-wide instead of segment-adjusted rates

### Capital Structure

**Modigliani-Miller (with taxes):** Debt creates value through the interest tax shield: `PV(Tax Shield) = T × D`.

**Tech company capital structure evolution:**
- Pre-revenue: ~100% equity (no debt capacity without revenue)
- Series B-D: Begin incorporating venture debt (10-20% of capital structure)
- Pre-IPO: Structured debt, convertibles, revolving credit facility
- Public SaaS maturity: Net Debt/EBITDA typically 0-2x

---

## 2. Financial Statement Analysis

### Income Statement (SaaS)

**Revenue:**
- Subscription revenue (recurring, high-predictability) — MONTHLY recurring revenue × 12 = ARR
- Professional services / implementation (lower margin, non-recurring)
- Usage/consumption (variable, high-growth potential)

**Gross Margin by Category:**
| Company Type | Gross Margin Target |
|-------------|---------------------|
| Pure SaaS (software only) | 75-85% |
| SaaS + PS blend | 60-75% |
| AI inference-heavy SaaS | 55-70% (inference COGS drag) |
| Marketplace | 40-60% |
| Hardware+software | 30-50% |

**AI/SaaS COGS components:**
- Cloud hosting (AWS/GCP/Azure)
- LLM inference costs (per-token; increasingly significant)
- Customer support (headcount + tools)
- Payment processing fees
- Third-party API costs
- Amortization of capitalized software development costs

### P&L Structure (SaaS)

```
Revenue
  - COGS (hosting, support, inference)
= Gross Profit (Target: 75-85% for pure SaaS)
  - Sales & Marketing (S&M)
  - Research & Development (R&D)
  - General & Administrative (G&A)
= EBITDA (adjusted: pre-stock compensation)
  - Depreciation & Amortization
= EBIT (Operating Income)
  - Interest and Other
= Pre-tax Income
  - Taxes
= Net Income
```

### Balance Sheet Analysis

**Key SaaS balance sheet ratios:**
- **Quick ratio:** (Cash + AR) / Current Liabilities > 1.0
- **Deferred revenue / ARR:** Growing deferred revenue signals strong prepaid annual bookings (bullish)
- **Prepaid expenses:** Large prepaid = advance commitment to vendors; watch for impairment risk
- **Net cash position:** Cash - Debt; target 12-24 months of runway at current burn

### Cash Flow Statement

**For SaaS companies, free cash flow is the most important metric:**
```
Free Cash Flow = Operating Cash Flow - CapEx
             ≈ Net Income + D&A + Stock Comp - Changes in Working Capital - CapEx
```

**SaaS Cash Flow timing advantage:** Annual prepaid subscriptions create positive working capital dynamics — customers pay upfront, creating deferred revenue that converts to cash before revenue is recognized.

---

## 3. Budgeting and Forecasting

### The Three Financial Models

**Bottom-up model (operational planning):**
- Build from individual department headcount plans + specific line items
- Most accurate for near-term (0-12 months)
- Used for: Board-approved operating plan, monthly variance analysis

**Top-down model (investor/strategic):**
- Market size → market share → implied revenue
- Used for: Investor pitch, TAM/SAM/SOM analysis, long-range planning

**Driver-based model (management planning):**
- Identify key business drivers (ARR per salesperson, conversion rates, NRR)
- Model scenarios by adjusting drivers
- Best for: Sensitivity analysis, headcount planning, go-to-market scenario planning

### SaaS Financial Model Architecture

```
INPUTS (drivers)
  - New ACV bookings per period
  - Win rate from pipeline
  - Average deal size
  - Sales cycle length
  - NRR / expansion assumptions
  - Churn rate by cohort
  ↓
MRR/ARR WATERFALL
  Beginning ARR
  + New ARR (new logos × ACV)
  + Expansion ARR
  - Churned ARR
  - Contracted ARR
  = Ending ARR
  ↓
INCOME STATEMENT
P&L built from ARR × gross margin - opex
  ↓
CASH FLOW BRIDGE
P&L → Operating CF → Free CF → Cash position
```

### Forecasting Best Practices

**Three-scenario planning:**
- **Base case:** Most likely outcome with current execution
- **Upside case:** With tailwinds (large deal, faster hiring, better conversion)
- **Downside case:** Macro deterioration, longer sales cycles, key employee departures

**Rolling 13-month forecast:** Update monthly. Incorporate actuals for completed months. More accurate than annual budget stuck in December.

**Leading vs. lagging indicators:**
- Lagging (report what happened): Revenue, churn, NRR
- Leading (predict what will happen): Pipeline, trial-to-paid conversion, product usage metrics

---

## 4. Management Accounting

### Unit Economics

**Customer Acquisition Cost (CAC):**
```
CAC = Total S&M spend / Number of new customers acquired
```
- Blended CAC: All customers regardless of channel
- Paid CAC: Customers acquired through paid channels only
- Organic CAC: ~$0 (benchmark by tracking separately)

**Customer Lifetime Value (LTV):**
```
LTV = ARPU × Gross Margin × (1 / Churn Rate)
```
Or for multi-year cohorts: LTV = Sum of discounted gross profit over expected customer life

**LTV:CAC ratio benchmarks:**
| Ratio | Interpretation |
|-------|---------------|
| < 1:1 | Destroying value on every customer — stop acquiring customers at this cost |
| 1:1 – 3:1 | Marginal — sustainable only with very fast payback |
| 3:1 | Minimum viable for Series A fundraising |
| 5:1+ | Excellent; room to accelerate marketing spend |
| >10:1 | Possibly underinvesting in growth |

**CAC Payback Period:**
```
CAC Payback = CAC / (ARPU × Gross Margin %)
```
Target: < 12 months for SMB; < 18 months for mid-market; < 24 months for enterprise.

### Rule of 40

A SaaS business is healthy when: Revenue growth % + FCF margin % ≥ 40

- **Benchmark:** 40+ is good; 50+ is excellent; 60+ is elite (Datadog, Snowflake, CrowdStrike territory)
- **Calculation note:** Use trailing 12-month growth rate and FCF margin
- Used by investors as a single screening metric; below 40 requires explanation

### Burn Multiple

```
Burn Multiple = Net Cash Burned / Net New ARR Added
```

| Range | Interpretation |
|-------|---------------|
| < 1x | Highly efficient; sustainable growth |
| 1-1.5x | Good; typical for well-run Series B |
| 1.5-2x | Acceptable; needs improvement |
| > 2x | Concerning; spending too much per dollar of ARR |
| > 3x | Unsustainable; address immediately |

**Context:** Burn multiple varies by stage — higher acceptable at pre-Series A (high investment in infrastructure) vs. Series C+ (should be converging on efficiency).

---

## 5. Tax Strategy for Tech Companies

### Section 174 R&D Expensing (2025 OBBBA Restoration)

**Critical 2025 Update:** The One Big Beautiful Budget Act (OBBBA), signed July 4, 2025, restored immediate expensing for domestic R&D:

**Section 174A (new):**
- Domestic R&D costs are **immediately deductible** (100% expensed in year incurred)
- Effective for tax years beginning July 4, 2025, and after
- Retroactive election available for tax years 2022-2024 (deadline July 6, 2026)
- Foreign R&D: Still subject to amortization over 15 years

**Before OBBBA (2022-2024):** R&D costs were required to be amortized over 5 years (domestic) or 15 years (foreign) under the TCJA amendment. This created massive cash flow drag for R&D-intensive companies — many startups owed tax despite having losses in traditional accounting.

**Action required:** If you have a calendar-year company that had domestic R&D expenses in 2022, 2023, or 2024, consult your tax advisor immediately about filing an amended return or making the retroactive election. The July 6, 2026 deadline is firm.

**What qualifies as R&D under Section 174:**
- Software development costs for new or improved software functionality
- Machine learning model training costs
- Experimental research and development
- Technology prototypes
- Note: "Funded research" (paid for by a customer under contract) generally does NOT qualify

### R&D Tax Credits (Section 41)

The Research Credit (R&D credit) is separate from Section 174 and provides:
- **Regular research credit:** 20% of qualifying research expenses above a base amount
- **Alternative Simplified Credit (ASC):** 14% of qualifying expenses above 50% of the average of the prior 3 years — simpler to calculate; preferred for most startups

**Startups payroll tax offset:** For qualified small businesses (< $5M gross receipts; < 5 years since first revenue), up to $500K/year of R&D credit can offset payroll taxes instead of income tax. This creates immediate cash value even for pre-profit companies.

### QSBS (Qualified Small Business Stock) — Section 1202

**OBBBA Expansion (2025):**
Pre-OBBBA limits: Asset cap $50M; exclusion cap $10M or 10× basis.

Post-OBBBA limits:
- **Asset cap:** Increased to $75M (qualifying company must have assets ≤$75M at time of issuance)
- **Exclusion cap:** Increased to $15M (or 10× adjusted basis, whichever is higher)
- **Phased exclusion:** 3-year hold: 50%; 4-year hold: 75%; 5-year+ hold: 100% federal capital gains excluded
- **Small business retroactivity:** Small businesses may elect to apply new rules retroactively (election window through the applicable deadline)

**QSBS requirements:**
- Domestic C-Corporation
- Active business (not professional services, finance, hospitality, farming)
- Must hold stock continuously from acquisition through sale
- Stock acquired at original issuance (not secondary market)
- Company must be a qualified small business at time of issuance

**Value at stake:** If you sell QSBS shares with $15M in gains after 5+ years, you pay zero federal capital gains tax. At 23.8% LTCG + NIIT rate, this saves $3.57M in federal taxes on a $15M gain.

**Founder guidance:**
- Confirm your stock qualifies at issuance (legal counsel)
- Maintain records of issue date, consideration paid, company assets at issuance
- Do not let QSBS qualification lapse (avoid issuing stock when assets > $75M)

### Delaware Franchise Tax

Delaware C-Corps pay annual franchise tax — potentially very high for startups:

**Authorized Shares Method (default but terrible for startups):**
- Based on number of authorized shares × assumed par value
- For a company with 10M authorized shares: can be $50K-$200K+ annually

**Assumed Par Value Capital Method (APVC — preferred):**
- Based on total assets / total shares × issued shares
- For typical startup: reduces to $400-$4,000/year

**Action:** Always use the APVC method when filing Delaware franchise tax. Mark this in your calendar for March 1 filing deadline.

### State Tax Nexus

Physical presence (office, employees) in a state creates income tax and sales tax nexus. With remote work:
- Employees in California → California income tax nexus + sales tax for CA customers
- Employees in New York → NYC and NY state nexus
- SaaS often subject to sales tax in ~30+ states (most states now tax digital services)

**Sales tax compliance:** Use Avalara or TaxJar for automated multi-state sales tax calculation, collection, and remittance. Manual compliance across 40+ states is not feasible without software.

---

## 6. Treasury Management

### Cash Management Framework

**Sweep accounts:** Excess operating cash should earn yield. Options:
- Treasury Money Market Funds (Fed-backed; highest safety; competitive yields ~4.5-5.0% in 2025)
- High-yield savings accounts (FDIC insured up to $250K; easy access)
- Treasury Direct: Direct US Treasury purchases (highest yield; 1-day settlement)
- Treasury bills (T-bills): 4-week, 13-week, 26-week maturity; highly liquid

**Banking diversification post-SVB:**
- Primary operating account: Large systemic bank (JPMorgan, Wells Fargo, Bank of America)
- For runway preservation: Treasury MMFs for amounts above FDIC limits
- Do NOT keep more than $250K in any single non-FDIC-insured bank account
- Startup-friendly banks: Silicon Valley Bank (acquired by First Citizens), Mercury, Brex

### Runway Calculation and Management

```
Runway = Cash Balance / Average Monthly Net Burn
```

Where: Net Burn = Total Cash Out - Total Cash In (include revenue)

**Runway targets by context:**
- Normal operations: 18-24 months of runway
- Fundraising environment: Start process with 12-18 months; complete before falling below 9 months
- Risk trigger: Below 6 months = emergency; begin cost reduction immediately

**Monthly burn monitor:**
```
Beginning Cash
+ Gross Cash In (revenue collections, investment)
- Gross Cash Out (payroll, COGS, vendors, CapEx)
= Ending Cash
= Net Burn (or Net Gain if cash flow positive)
```

---

## 7. Debt Financing

### Venture Debt Market (2025)

US venture debt hit **$68.8 billion in 2025** (PitchBook/Runway Growth Capital data). Venture debt is now a standard part of startup capital strategy.

**When to use venture debt:**
- Extend runway between equity rounds without dilution
- Fund specific assets or programs (bridge to milestone; equipment purchase)
- After closing equity round (lenders follow equity; lend alongside or after)
- When you have 12+ months of cash (lenders won't lend to distressed companies)

**Do NOT use venture debt:**
- To delay a necessary down round (debt makes the eventual crisis worse)
- When ARR growth is negative (you won't be able to service it)
- Without a credible plan for the debt maturity date

### Venture Debt Structures

**Term Loan:**
- Fixed amount; fixed term (typically 24-48 months)
- Monthly principal + interest payments
- Typical rate: Prime + 2-5% or SOFR + 3-6% (varies by stage and lender)
- Typically 10-30% of last equity round size

**Revenue-Based Financing:**
- Advance against future revenue; repaid as % of monthly revenue
- No fixed maturity; payment varies with revenue
- Higher effective cost (1.2-1.5x advance amount)
- Works well for SaaS companies with predictable ARR

**Revolving Credit Facility (Revolver):**
- Draw up to limit; repay; draw again
- Typically collateralized by ARR or accounts receivable
- Common at late stage / pre-IPO
- Provides working capital flexibility without permanent dilution

### Debt Covenants

Common venture debt covenants:
- **Minimum cash covenant:** Maintain minimum $X cash at all times (typically 25-50% of loan balance)
- **ARR covenant:** Maintain minimum ARR level; breach triggers default
- **Leverage ratio:** Total debt / trailing 12-month revenue
- **No material adverse change (MAC):** Lender can accelerate if MAC occurs

**Covenant violation consequences:** Technical default; lender can demand immediate repayment. Negotiate cure periods and covenant-lite structures where possible.

---

## 8. M&A Fundamentals

### Valuation Methods for Tech Companies

**Comparable Company Analysis (Comps):**
- Select 8-12 comparable public companies
- Apply relevant multiples to your metrics
- 2025 SaaS valuation multiples (NTM Revenue):
  - Hypergrowth (>40% YoY): 7-10x NTM revenue
  - High growth (20-40%): 4-7x NTM revenue
  - Moderate growth (10-20%): 2-4x NTM revenue
  - < 10% growth: 1-2x NTM revenue (on metrics like EBITDA)
- Adjust for rule of 40 score; NRR; gross margin; market position

**Precedent Transactions:**
- Recent M&A transactions in your sector
- Typically carry 20-30% premium over public comps (control premium)
- 2024-2025 AI company acquisitions: 15-25x revenue common for frontier AI assets

**DCF (Discounted Cash Flow):**
- Project free cash flows 5-10 years; apply terminal value
- Discount at WACC
- Highly sensitive to assumptions; best used as sanity check
- Less reliable for high-growth companies where most value is in terminal value

### M&A Process Overview

**Sell-side process (raising M&A alternatives):**
1. Preparation (3-6 months): Financial model; CIM (confidential information memorandum); virtual data room
2. Outreach (1-2 months): Investment banker identifies 20-40 strategic + financial sponsors
3. First round bids: Indication of interest (non-binding)
4. Management presentations: Finalist 3-5 parties
5. Final bids: Binding letters of intent
6. Exclusivity + due diligence (45-90 days)
7. Definitive agreement + closing (30-60 days post-exclusivity)

**Key deal protections (seller):**
- Representations & Warranties Insurance (RWI): Insures against rep breaches; eliminates escrow
- Escrow: Standard 10-15% of purchase price held 12-18 months for indemnification
- Earnout: Future payment tied to performance milestones (up to 20-30% of deal value)

---

## 9. IPO Readiness

### IPO Readiness Timeline

**18-24 months pre-IPO:**
- SOC 2 Type II audit (if not complete)
- CFO upgrade to public company CFO caliber
- Begin recruiting audit committee members with public company experience
- Upgrade ERP system (QuickBooks → NetSuite; Sage Intacct for mid-market)
- Implement financial close discipline (target 10 business days to close quarterly)

**12-18 months pre-IPO:**
- Select Big Four auditor; begin audit of restated financials
- Implement Section 404 internal controls over financial reporting (early SOX)
- Build investor relations capability
- Conduct S-1 drafting dry run

**6-12 months pre-IPO:**
- File confidential S-1 with SEC (allows testing disclosures)
- Begin equity research analyst relationship-building
- Conduct 1-2 IPO-readiness dry runs with CFO/CEO
- Resolve all open accounting questions (revenue recognition, stock comp, segment reporting)

**Early SOX implementation benefit:** Companies that implement SOX-level internal controls 18-24 months before IPO experience 57% fewer material weakness disclosures post-IPO (industry directional guidance; treat as qualitative, not precise statistic).

### IPO Readiness Metrics

Typical IPO thresholds (2025-2026 environment):
- **Revenue:** $150-250M ARR minimum for NASDAQ; $100M+ for NYSE (both prefer $250M+)
- **Growth rate:** 30%+ YoY preferred; 20%+ acceptable with strong unit economics
- **Gross margin:** 70%+ for SaaS
- **NRR:** 120%+ preferred
- **Rule of 40 score:** 40+ required; 50+ preferred
- **Profitability path:** Must show credible path to GAAP profitability within 2-3 years post-IPO

---

## 10. FinOps and Cloud Cost Optimization

### The Cloud Spending Context

Public cloud spending hit **$723.4 billion in 2025** (Gartner). Cloud costs are frequently the #2 or #3 expense line for SaaS companies after payroll.

### FinOps Framework

**Inform:**
- Tag all cloud resources: team, environment, product, cost center
- Allocate 100% of cloud spend to business unit (showback → chargeback)
- Unit economics: Cost per customer, per API call, per GB stored
- Anomaly alerts: > 20% week-over-week spike

**Optimize:**
| Optimization | Typical Savings |
|-------------|----------------|
| Reserved instances / Savings Plans | 30-60% vs. on-demand |
| Spot/preemptible instances (batch/ML) | 60-90% vs. on-demand |
| Right-sizing over-provisioned resources | 20-30% reduction |
| Idle resource elimination | 10-20% of total bill |
| Storage lifecycle policies | 40-60% on cold data |

**Operate:**
- Monthly FinOps review: teams present cost trends + initiatives
- Unit cost targets: Set budget for cost/unit metric; track weekly
- Commitment hierarchy: Spot → Reserved → On-demand (use in that priority order)

### AI/LLM Cost Management

New cost category requiring specialized FinOps as of 2025:

- **LLM inference costs:** Track cost per API call; per user; per feature separately
- **Model tier routing:** Haiku for simple tasks, Sonnet for medium, Opus for complex — saves 5-10x cost
- **Prompt caching:** 80-90% savings on repeated context (implement on all high-volume endpoints)
- **Batch API:** 50% savings on non-real-time workflows (reports, analysis, background processing)
- **Budget guards:** Hard limits per user per day to prevent runaway consumption
- **Benchmark:** AI inference COGS should be < 20% of gross revenue for sustainable unit economics

---

## 11. SaaS Revenue Accounting (ASC 606)

### The Five-Step Framework

**Step 1 – Identify the contract with the customer:**
Contract = agreement with enforceable rights and obligations. Both parties must commit; payment terms must be present. Does NOT need to be written.

**Step 2 – Identify the performance obligations:**
Performance obligation = promise to transfer a distinct good or service. Distinct = customer can benefit from it alone or with other readily available resources AND it's separately identifiable in the contract.

Example: SaaS subscription + implementation services = 2 performance obligations if implementation is distinct.

**Step 3 – Determine the transaction price:**
Total consideration you expect to receive. Include variable consideration (usage fees, bonuses, penalties) but only to the extent it is highly probable it will not be reversed.

**Step 4 – Allocate the transaction price:**
Allocate based on relative standalone selling price (SSP) of each performance obligation.

Example: $120K total contract = $100K software (SSP) + $20K implementation (SSP) → allocate pro-rata based on SSP ratios.

**Step 5 – Recognize revenue when/as performance obligations are satisfied:**
- SaaS subscriptions: Recognized ratably over the subscription period (monthly)
- Implementation services: Point-in-time (on acceptance) or over-time if customer controls the service as delivered
- Upfront fees: Recognize over expected customer relationship period if they are set-up fees tied to the contract

### Deferred Revenue and Contract Liability

Annual prepaid subscriptions create deferred revenue:
- Customer pays $120K on January 1 for 12-month subscription
- January: Record $120K cash; $120K deferred revenue
- Each month: Recognize $10K revenue; reduce deferred revenue by $10K
- December 31: $0 deferred revenue; $120K revenue recognized

Deferred revenue on balance sheet = future revenue "backlog." Growing deferred revenue is bullish; shrinking may signal billing/churn issues.

---

## 12. Investor Relations

### Investor Update Cadence

**Monthly (for seed/Series A):**
- Key metrics dashboard (ARR, MRR, churn, NRR, cash, runway)
- Top wins and challenges
- 3 things you need from investors
- One paragraph on business narrative progress

**Quarterly (for Series B+):**
- Full financial statements vs. plan
- Business metrics deep dive
- Strategic update
- Guidance for next quarter

### Fundraising Metrics Package

For Series A fundraising, investors expect:
- MRR/ARR with monthly history (12+ months)
- MRR growth rate and acceleration/deceleration
- Cohort retention curves
- LTV:CAC ratio and payback period
- Gross margin trend
- Burn rate and runway
- Pipeline and conversion metrics
- NPS or customer satisfaction data

**NRR is the single most scrutinized metric at Series A.** NRR > 120% significantly improves valuations. NRR < 100% is a deal-killer unless you have an exceptional explanation.

---

## 13. Financial Risk Management

### Risk Framework

**Market risk:**
- Interest rate risk: Variable-rate debt; mitigate with fixed-rate conversion or interest rate swaps
- FX risk: Revenue in foreign currencies; natural hedging first; forward contracts for >$5M exposure
- Equity market risk: Employee equity plans; 409A volatility in down markets

**Credit risk:**
- Customer credit: AR aging analysis; watch for concentration (single customer >20% of ARR)
- Vendor risk: Critical vendor dependency; SLA protections; backup vendor relationships

**Liquidity risk:**
- Maintain 18-24 months runway minimum
- Monitor covenant compliance weekly
- Maintain undrawn revolver if available

**Operational risk:**
- Fraud prevention: Segregation of duties; dual approval for wire transfers > $10K
- Cyber risk: Cyber insurance; regular penetration testing
- Business continuity: Disaster recovery for financial systems; tested annually

### Insurance Portfolio for Tech Companies

| Coverage | Target Amount | Priority |
|----------|-------------|---------|
| D&O | $5M (seed) → $50M+ (pre-IPO) | Critical |
| Cyber liability | $1-10M | Critical |
| E&O / Professional liability | $1-5M | Critical |
| General liability | $1-2M | Required |
| Employment practices | $1-3M | Required |
| Workers' comp | State minimum | Required |
| Business interruption | Revenue × 3-6 months | Important |

---

## Quick Reference: Finance Decision Matrix

| Situation | Action |
|-----------|--------|
| Evaluating capital investment | Calculate NPV; accept if > 0 and IRR > WACC |
| Financing decision | Equity first; venture debt to extend runway (< 20% dilution equivalent) |
| Domestic R&D 2022-2024 | File retroactive Section 174A election by July 6, 2026 |
| QSBS planning | Confirm stock qualifies; track 5-year hold date; maximize to new $15M exclusion cap |
| Pricing SaaS to enterprise | Value-based; 10x ROI rule; include ROI quantification in pitch |
| Measuring business health | Rule of 40 (growth% + FCF%) ≥ 40; NRR > 110% |
| Managing cash | Treasury MMFs above FDIC limits; minimum 18-month runway |
| Preparing for fundraising | 12+ months of metrics history; NRR is most scrutinized |
| IPO prep | Start 18-24 months early; Big Four auditor; SOX controls; close in 10 business days |
| Cloud costs growing | FinOps: tag all resources; implement reserved instances; AI cost routing |
