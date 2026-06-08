---
name: business-finance
description: Expert-level skill covering corporate finance, financial modeling, SaaS unit economics, and AI company financial structures. Covers WACC/CAPM, DCF/NPV/IRR, FP&A driver-based modeling, SaaS metrics (NRR/GRR/LTV:CAC/Rule of 40), AI gross margin dynamics, GPU infrastructure financing, ASC 606 revenue recognition, and M&A/LBO modeling. Use this when building financial models, analyzing investment decisions, structuring deals, evaluating company health, or communicating financial performance.
---

# Business Finance Expert

## Core Corporate Finance: The Foundational Framework

### Time Value of Money

The fundamental principle: a dollar today is worth more than a dollar tomorrow because it can be invested to earn a return. All valuation flows from this.

**Present Value (PV):**
```
PV = CF_t / (1 + r)^t
```

**Net Present Value (NPV):**
```
NPV = Σ [CF_t / (1 + r)^t] - Initial Investment
```
- NPV > 0: invest (creates value)
- NPV < 0: reject (destroys value)
- NPV = 0: indifferent (earns exactly the cost of capital)

**Internal Rate of Return (IRR):**
The discount rate at which NPV = 0. Solve for r in the NPV equation.
- IRR > hurdle rate → proceed
- IRR < hurdle rate → reject
- **IRR fallacy:** IRR assumes reinvestment at the IRR rate — unrealistic for high-IRR projects. Use Modified IRR (MIRR) for more accurate reinvestment assumption.

---

## WACC (Weighted Average Cost of Capital)

The discount rate used in DCF. Represents the blended cost of equity and debt financing, weighted by their proportions in the capital structure.

```
WACC = (E/V) × Ke + (D/V) × Kd × (1 - T)
```

Where:
- E = market value of equity
- D = market value of debt
- V = E + D (total firm value)
- Ke = cost of equity (from CAPM)
- Kd = pre-tax cost of debt
- T = marginal corporate tax rate

### CAPM (Capital Asset Pricing Model) — Cost of Equity

```
Ke = Rf + β × (Rm - Rf)
```

Where:
- Rf = risk-free rate (10-year US Treasury yield; ~4.2% as of June 2026)
- β (Beta) = systematic risk of the equity relative to the market
- Rm - Rf = equity risk premium (ERP; ~5.5–6.0% for US markets per Damodaran 2025)
- β × (Rm - Rf) = company-specific risk premium above the risk-free rate

**For early-stage tech/AI companies:**
- Unlevered beta (asset beta) for comparable public companies: 1.3–1.8 for SaaS, 1.5–2.2 for AI infrastructure
- Typical WACC for early-stage AI startup (pre-revenue): 25–40% (reflecting high systematic risk, illiquidity premium, and execution risk)
- Typical WACC for growth-stage SaaS (Series B/C): 15–25%

---

## DCF Valuation: Building the Model

### The Five-Step DCF

1. **Project free cash flows** (FCF) for explicit forecast period (typically 5–10 years)
2. **Calculate terminal value** (value beyond forecast period)
3. **Discount all cash flows** at WACC
4. **Sum to get Enterprise Value** (EV)
5. **Bridge to Equity Value**: EV - Net Debt + Cash

**Free Cash Flow to Firm (FCFF):**
```
FCFF = EBIT × (1 - Tax Rate) + D&A - ΔWorking Capital - CapEx
```

**Terminal Value (Gordon Growth Model):**
```
TV = FCF_final × (1 + g) / (WACC - g)
```
Where g = perpetuity growth rate (typically 2–3% for mature businesses; 1–2% for conservative AI/tech)

**Warning:** Terminal value typically represents 60–80% of DCF value for high-growth companies. A ±0.5% change in terminal growth rate can swing valuation 20–30%. Sensitivity tables are not optional.

### AI Company DCF Considerations

Traditional DCF struggles with AI companies because:
- High current burn, low near-term FCF
- Gross margins volatile (GPU costs, API costs)
- Revenue model may shift (usage → subscription → outcome-based)
- Option value of future products significant

**Adjustments:**
- Use scenario-weighted DCF (base, bull, bear cases)
- Explicitly model GPU cost reduction trajectory (GPU pricing has fallen ~35%/year on TPU/A100 equivalent)
- Include competitive moat degradation assumptions in terminal period
- For pre-revenue: use comparable company multiples (ARR multiple) rather than DCF

---

## SaaS Unit Economics: The Complete Framework

### Revenue Metrics

**ARR (Annual Recurring Revenue):**
```
ARR = Σ (monthly recurring revenue × 12) for all active subscriptions
```
Exclude one-time fees, professional services, variable usage above minimum commitment.

**MRR Components (the waterfall):**
```
Beginning MRR
+ New MRR         (new logos)
+ Expansion MRR   (upsell, cross-sell, seat adds)
- Contraction MRR (downgrades)
- Churn MRR       (cancellations)
= Ending MRR
```

**Net Revenue Retention (NRR) / Net Dollar Retention (NDR):**
```
NRR = (Beginning ARR + Expansion - Contraction - Churn) / Beginning ARR
```
- NRR > 100%: You grow revenue even with no new customers
- Best-in-class: Snowflake ~158%, Datadog ~130%, Twilio ~120%
- Minimum for Series A/B: 100%+; investors increasingly want 110%+
- NRR < 100%: Negative cohort economics. You are on a leaking ship.

**Gross Revenue Retention (GRR):**
```
GRR = (Beginning ARR - Contraction - Churn) / Beginning ARR
```
- Cannot exceed 100% (no expansion in GRR)
- Reflects baseline retention quality
- Best-in-class: 90–95%. Below 80% = serious concern.

### Customer Acquisition Metrics

**CAC (Customer Acquisition Cost):**
```
CAC = Total Sales & Marketing Spend / New Customers Acquired
(in the same period, typically quarterly)
```

**LTV (Lifetime Value):**
```
LTV = ARPU × Gross Margin % × (1 / Churn Rate)
```
- For SaaS: LTV = (Annual ARPU × Gross Margin %) / Annual Churn Rate

**LTV:CAC Ratio:**
- Below 3:1: You're spending too much to acquire customers relative to their value
- 3:1 to 5:1: Healthy, worth scaling
- Above 5:1: May be underinvesting in growth (could acquire faster)

**CAC Payback Period:**
```
CAC Payback = CAC / (ARPU × Gross Margin %)
```
- Under 12 months: World-class
- 12–18 months: Healthy
- 18–24 months: Acceptable with strong NRR
- Over 24 months: Capital-intensive; need strong balance sheet

### Efficiency Metrics

**Burn Multiple:**
```
Burn Multiple = Net Burn / Net New ARR
```
- Best-in-class: <1x (every dollar of burn generates >$1 of new ARR)
- Good: 1–1.5x
- Average: 1.5–2x
- Concerning: >2x
- YC/Sequoia threshold for Series A: typically <2x

**Rule of 40:**
```
Rule of 40 = ARR Growth Rate (%) + EBITDA Margin (%)
```
- The benchmark: 40+ is healthy for SaaS
- Growth-stage companies (pre-$50M ARR): typically achieve via high growth, negative margin
- Scale companies ($100M+ ARR): need to balance both
- AI companies in 2025: typically 20–35 (GPU costs and R&D suppress margin)

**Magic Number (Sales Efficiency):**
```
Magic Number = (Q Ending ARR - Q Beginning ARR) × 4 / Prior Q Sales & Marketing Spend
```
- > 1.0: Highly efficient; add sales resources aggressively
- 0.75–1.0: Good; grow sales team moderately
- < 0.5: Broken GTM; fix before scaling spend

---

## AI Company Gross Margin Structure

AI companies have fundamentally different gross margin dynamics from traditional SaaS:

### Cost-of-Revenue Components

**Traditional SaaS COGS:**
- Hosting/infrastructure: 8–15%
- Customer support: 3–7%
- Gross margin: 70–85%

**AI SaaS COGS (as of 2026):**
- AI model API costs (if using proprietary models): 15–35%
- GPU inference infrastructure (if self-hosting): 10–25%
- Data processing and storage: 3–8%
- Customer support: 3–6%
- **Gross margin: 40–65%** (significantly lower than traditional SaaS)

**Foundation model providers (OpenAI, Anthropic, Google):**
- Hardware costs, training amortization, cooling: 30–45% of revenue
- Gross margin: 50–65% depending on model tier

**Vertical AI applications (Harvey, Sierra, Glean):**
- Higher value = higher pricing = gross margin recovery
- Harvey: ~65–70% gross margin (legal vertical commands premium pricing)
- Target for AI SaaS: 60%+ gross margin at scale

### Gross Margin Recovery Levers

1. **Model cost optimization**: Route to cheaper models for simple tasks (GPT-4o-mini vs GPT-5)
2. **Prompt caching**: 60–80% input token cost reduction for stable system prompts
3. **Batching**: Non-real-time inference jobs processed in off-peak batches at lower compute cost
4. **Fine-tuning**: Fine-tune a smaller model for your specific task → match frontier quality at 10–20% cost
5. **Self-hosting**: At sufficient scale, hosting OSS models (Llama 4) cheaper than API
6. **Output length optimization**: Tune prompts and sampling parameters to reduce output token count

---

## AI Infrastructure Financing

### GPU Cost Structure

**Spot vs. Reserved vs. On-Demand (AWS A100/H100 equivalent, 2026):**
- On-demand: ~$3.50–$4.00/hr per H100
- 1-year reserved: ~$2.00–$2.50/hr per H100 (35–45% discount)
- 3-year reserved: ~$1.40–$1.80/hr per H100 (50–60% discount)
- Spot: ~$1.00–$1.50/hr per H100 (50–70% discount, interruptible)

**Cloud committed use credits:**
- AWS, GCP, Azure offer $250K–$500K credits for startups via partner programs
- Standard stack: $250K AWS Activate + $250K GCP (Google for Startups) + $250K Azure (Microsoft for Startups) = ~$750K total credits
- Timing: Secure all three before your seed round; credits reduce burn during product-market fit search

### GPU-Secured Lending

For AI companies that own or lease significant GPU infrastructure:
- Lenders (TriplePoint, Hercules, Silicon Valley Bank successors, Coatue-backed lenders) now provide **GPU-secured venture debt**
- LTV: 60–75% of GPU fleet fair market value
- Terms: 24–36 month term, interest-only period 6–12 months, then amortization
- Rate: Prime + 3–5% (floating); approximately 9–12% in 2026
- **Use case:** Bridge capital between equity rounds; avoid dilution during GPU-intensive training runs

### Hyperscaler Committed Spend (HCS)

Microsoft-OpenAI, Google-Anthropic, Amazon-Anthropic committed partnerships reflect a new financing model:
- Hyperscalers invest in AI companies AND receive committed cloud spend credits
- AI companies use hyperscaler credits to fund compute at below-market rates
- **Implication for startups:** Negotiating an enterprise agreement with hyperscalers can include compute credits as part of the deal. Ask.

---

## FP&A: Driver-Based Financial Modeling

### Driver-Based Model Architecture

Traditional FP&A: top-down percentage growth on prior year. Driver-based: model the *physics* of your business.

**For a SaaS company, the drivers:**
```
Revenue = [Beginning ARR + New ARR + Expansion ARR - Churn ARR - Contraction ARR] / 12
New ARR = Qualified Pipeline × Win Rate × Average Deal Size
Pipeline = Marketing Leads × Lead-to-Opp Conversion × Opp Quality Score
Expansion = Eligible Customer Base × Upsell Rate × Average Expansion Amount
Churn = At-Risk ARR × Churned %
```

**For an AI usage-based company:**
```
Revenue = Active Users × Avg Queries/User/Month × Average Revenue/Query
Active Users = Beginning Users + New Users - Churned Users
New Users = New Signups × Activation Rate
Avg Revenue/Query = f(model tier mix, caching efficiency, batch vs. real-time)
```

### Three-Statement Model Integration

**Income Statement → Balance Sheet → Cash Flow Statement** must reconcile:
```
Net Income → Retained Earnings (Balance Sheet equity)
D&A (I/S add-back) + ΔWorking Capital + CapEx → Cash from Operations (CFS)
Ending Cash (CFS) = Cash on Balance Sheet
```

Never deliver a financial model where the balance sheet doesn't balance. Auditors and sophisticated investors will find it immediately.

### Scenario Planning

Build at minimum 3 scenarios:
- **Base case**: Management plan
- **Bear case**: 30% revenue miss, 15% cost overrun (key assumption: churn doubles)
- **Bull case**: 20% revenue beat (key assumption: viral loop accelerates)

Vary the 2–3 drivers that have the highest sensitivity. Use tornado charts for sensitivity visualization.

---

## Revenue Recognition: ASC 606

### The Five-Step Model

ASC 606 (US GAAP) and IFRS 15 (international) use the same five-step framework:

**Step 1: Identify the contract(s) with a customer**
- Written or oral; must have commercial substance
- Both parties must have approved

**Step 2: Identify the performance obligations**
- Each distinct promised good or service = a performance obligation
- SaaS: (a) software license, (b) hosting, (c) support, (d) professional services — may be bundled or separate POBs

**Step 3: Determine the transaction price**
- Fixed consideration + variable consideration (estimated, constrained)
- Variable consideration: usage fees, bonuses, refunds — estimate using expected value or most likely amount

**Step 4: Allocate the transaction price**
- Allocate to each POB based on relative Standalone Selling Price (SSP)
- SSP = price when sold separately; use observable prices when available

**Step 5: Recognize revenue as performance obligations are satisfied**
- **Over time**: Hosting, subscriptions, support (ratable, straight-line over contract term)
- **Point in time**: Software licenses (most), professional services with discrete deliverables

### Deferred Revenue and Billings

**Deferred revenue** = cash received / billed but not yet earned. Common in SaaS with annual upfront billing.
- Annual billing, $120K/year contract → $120K deferred revenue on day 1, recognize $10K/month
- On the balance sheet: current (earned within 12 months) vs. non-current

**Billings** = invoice amount in period. For SaaS analysis: Billings > Revenue → improving business (growing backlog). Billings < Revenue → burning down backlog (shrinking).

---

## LBO Modeling and M&A Finance

### LBO (Leveraged Buyout) Model Structure

```
Sources & Uses
─────────────────────────────────────
Sources:                   Uses:
Senior Debt    55%         Purchase Price  90%
Mezz Debt      15%         Fees/Expenses    5%
Equity         30%         Working Capital  5%
Total         100%         Total          100%
```

**The debt repayment schedule:**
- Senior debt: amortizes 5–10% per year; remaining balance repaid at exit
- Mezzanine (second lien or subordinated): interest only, balloon at maturity
- **Covenant compliance**: Leverage ratio (Total Debt/EBITDA), Interest Coverage (EBITDA/Interest Expense) — breach triggers default

**Exit multiple and IRR:**
```
Entry EV/EBITDA: 8–12x (SaaS: 10–20x forward ARR)
Exit multiple:   Assume same as entry (conservative) or 1–2x expansion if growth sustained
IRR = solve for r: Equity Invested × (1+r)^n = Equity Exit Value
```
- Typical PE target IRR: 20–25%+ for buyout; 30%+ for venture-style

### M&A: Earnouts and ASC 805

**Earnout**: Contingent consideration paid to sellers if performance milestones are met post-close. Common in AI/tech acquisitions where valuation gap exists.

**ASC 805 (Business Combinations):** Earnouts must be recorded at fair value on acquisition date. Subsequently remeasured each period through earnings (P&L impact). A falling earnout probability = a gain to the acquirer (and vice versa). This creates earnings volatility post-acquisition.

**Earnout best practices:**
- Keep earnout period short (12–24 months max)
- Use objective, auditable metrics (ARR, gross profit)
- Define what happens if the acquirer's actions affect attainment (buyer-friendly vs. seller-friendly language)
- Cap at 20–30% of total deal value to avoid perverse incentives

---

## Investment Metrics: EVA and DuPont

### EVA (Economic Value Added)

```
EVA = NOPAT - (Invested Capital × WACC)
```
Where NOPAT = Net Operating Profit After Tax.

EVA > 0: Company is creating value above its cost of capital.
EVA < 0: Destroying value (even if profitable under GAAP).

**Why EVA matters for AI companies:** Many AI companies are GAAP-profitable (or near it) but have EVA < 0 because their capital cost (funding, GPU infrastructure) is high. Investors focused on EVA may have a more skeptical view than ARR-multiple investors.

### DuPont Analysis (Return on Equity Decomposition)

```
ROE = Net Profit Margin × Asset Turnover × Financial Leverage
    = (Net Income / Revenue) × (Revenue / Assets) × (Assets / Equity)
```

The three levers:
- Improve profitability (margin expansion)
- Improve efficiency (generate more revenue per dollar of assets)
- Increase leverage (use debt — increases risk alongside return)

For early-stage AI companies: ROE is typically negative (losses). The DuPont framework becomes useful at scale to diagnose which lever needs attention.

---

## Finance Communication: Investor Reporting Standards

### Board Package Metrics (Series A/B)

```
Financial Summary
─────────────────────────────
ARR:              $X.XM (+XX% YoY)
MRR Growth:       +$XXK MoM
NRR:              XXX%
Gross Margin:     XX% (trending +/-)
Burn Rate (net):  $XXK/month
Runway:           XX months
Cash:             $X.XM

Unit Economics
─────────────────────────────
LTV:CAC:          X.Xx
CAC Payback:      XX months
Burn Multiple:    X.Xx
Rule of 40:       XX
```

### Fundraising Data Room Contents

```
Financial models (3-statement, 36-month, with actuals)
Historical P&L by month (last 24 months minimum)
ARR/MRR cohort analysis (logo retention, dollar retention by cohort vintage)
Capitalization table (fully diluted, pre-money and post-money)
Revenue waterfall (new, expansion, contraction, churn by month)
Top 10 customer breakdown (ARR, tenure, product usage)
Pipeline CRM export (stage, value, close date, age)
Prior investor agreements (SAFEs, notes, term sheets)
Bank statements or financial statements (audited if available)
```
