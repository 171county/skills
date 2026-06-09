---
name: business-finance-expert
description: Expert-level business finance knowledge for tech companies covering three-statement financial modeling, SaaS metrics (ARR/MRR/NRR/GRR/churn), unit economics (CAC/LTV/Burn Multiple/Magic Number/Rule of 40), FP&A (rolling forecasts, variance analysis, headcount planning), cash flow management, valuation (DCF, ARR multiples, EBITDA), ASC 606 revenue recognition, ASC 350-40 capitalized software, tax strategy (QSBS, R&D credits, Section 174), AI company-specific financials (GPU costs, inference COGS, AI gross margins), and SOX compliance. Use for any financial modeling, metric analysis, fundraising prep, or financial strategy question.
---

# Business Finance Expert

## Three-Statement Financial Model

### Income Statement (P&L)

```
Revenue
  Software subscriptions (ARR/12)
  Professional services
  Usage-based revenue
  
  Less: Cost of Goods Sold (COGS)
    Infrastructure / hosting
    Customer support headcount
    Payment processing fees
    Third-party API costs (LLM inference for AI companies)
    
= Gross Profit
  Gross Margin % = Gross Profit / Revenue × 100
  
  Operating Expenses:
    Research & Development (R&D)
      - Engineering headcount
      - Capitalized software dev costs (per ASC 350-40)
    
    Sales & Marketing (S&M)
      - Sales headcount + commissions
      - Marketing programs, events, tools
      - Demand generation
    
    General & Administrative (G&A)
      - Finance, HR, Legal headcount
      - Facilities, insurance, equipment
      
= EBITDA (Earnings Before Interest, Taxes, Depreciation & Amortization)

  Less: D&A (Depreciation & Amortization)
    - Capitalized software amortization
    - Hardware depreciation
    - M&A-related amortization of intangibles
    
= EBIT (Operating Income)

  Less: Interest expense / Plus: Interest income
= EBT (Earnings Before Taxes)
  Less: Income taxes
= Net Income
```

### Balance Sheet

```
ASSETS
  Current Assets:
    Cash and cash equivalents
    Accounts Receivable (AR)
    Prepaid expenses
    Deferred contract costs (ASC 606)
    
  Non-Current Assets:
    Capitalized software development costs
    Property and equipment (net)
    Right-of-use assets
    Goodwill (from acquisitions)
    Acquired intangible assets (net)
    
= Total Assets

LIABILITIES
  Current Liabilities:
    Accounts Payable (AP)
    Accrued expenses
    Deferred revenue (unearned subscriptions — LIABILITY)
    Current portion of debt
    
  Non-Current Liabilities:
    Long-term debt
    Deferred tax liabilities
    Long-term lease obligations
    
EQUITY
  Common stock + APIC
  Accumulated other comprehensive income
  Retained earnings (deficit)
  
= Total Liabilities + Equity
```

**Key balance sheet relationships:**
- High deferred revenue = customers paying in advance = positive working capital signal
- Growing AR with slower revenue growth = collections problem
- Negative retained earnings (deficit) = normal for growth-stage tech companies

### Cash Flow Statement

```
Operating Cash Flow (OCF):
  Net Income (Loss)
  Add back: D&A, stock-based compensation (non-cash)
  Changes in working capital:
    + Increase in deferred revenue (cash received, not yet earned)
    + Increase in AP (paying later = keeping more cash)
    - Increase in AR (earned but not yet collected)
    - Increase in prepaid expenses (cash paid in advance)
    
= Operating Cash Flow

Investing Cash Flow:
  - Capitalized software development costs (ASC 350-40)
  - Capital expenditure (hardware, equipment)
  - Acquisitions (net of cash acquired)
  
= Investing Cash Flow

Financing Cash Flow:
  + Debt proceeds
  - Debt repayments
  + Equity issuances (net of fees)
  - Share repurchases
  
= Financing Cash Flow

Net Change in Cash = OCF + ICF + FCF
Ending Cash = Beginning Cash + Net Change
```

**Rule of thumb:** Free Cash Flow (FCF) = OCF - CapEx. FCF margin = FCF / Revenue. World-class SaaS at scale: 25–35% FCF margin.

---

## SaaS Metrics

### ARR/MRR Decomposition

**ARR Waterfall (monthly reconciliation):**
```
Beginning ARR
+ New ARR          (new customers acquired this period)
+ Expansion ARR    (upsell, cross-sell, seat additions)
- Contraction ARR  (downgrades, seat reductions)
- Churned ARR      (cancellations)
= Ending ARR

Net New ARR = New + Expansion - Contraction - Churn
```

**What's in vs. out of ARR:**
- IN: Recurring subscription fees, platform fees, committed seat-based fees
- OUT: One-time implementation/professional services fees, variable usage above contract minimum, hardware sales

### Net Revenue Retention (NRR)

```
NRR = (Beginning ARR + Expansion ARR - Contraction ARR - Churned ARR) / Beginning ARR × 100
```

**Benchmarks:**
| Category | NRR |
|----------|-----|
| World-class (Snowflake, Datadog peak) | >130% |
| Strong growth SaaS | 110–130% |
| Solid | 100–110% |
| Breaking even on existing base | ~100% |
| Shrinking customer base | <100% |

NRR >100% means even with zero new customer acquisition, revenue grows from existing customers.

### Gross Revenue Retention (GRR)

```
GRR = (Beginning ARR - Contraction ARR - Churned ARR) / Beginning ARR × 100
```

GRR measures pure retention excluding expansion. Ceiling = 100%.

**Benchmarks:**
- Best-in-class enterprise: >95%
- Strong enterprise: 90–95%
- SMB: 80–88% is acceptable
- Below 80%: Significant retention problem

**NRR vs. GRR distinction:** A company can have GRR of 85% (losing customers) but NRR of 115% (remaining customers expand enough to offset). Analyze both.

### Cohort Analysis

Track each monthly customer cohort independently:

```
Cohort     M0    M1    M3    M6    M12   M24
Jan-24    100%   94%   88%   82%   75%   68%
Feb-24    100%   95%   90%   85%   78%    -
Mar-24    100%   93%   87%   83%    -     -
```

**Logo churn vs. revenue churn:** Losing small customers while retaining large accounts = high logo churn, but good revenue retention. Focus on revenue churn for SaaS businesses.

**Annual logo churn benchmarks:**
- Enterprise (>$50K ACV): <5%
- Mid-market ($10K–$50K ACV): 8–15%
- SMB (<$10K ACV): 15–30% (acceptable if new customer volume is high)

---

## Unit Economics

### CAC (Customer Acquisition Cost)

```
CAC = Total S&M Spend / New Customers Acquired

Include in S&M: Salaries + commissions + benefits of S&M team,
advertising spend, events, marketing tools, trade shows, 
PR/content, demand gen programs.

Apply a lag: For 6-month sales cycles, use S&M spend from 
6 months ago to match against new customers this month.
```

**CAC by channel:** Calculate separately for outbound (SDR-sourced), inbound (marketing-sourced), PLG (product-led self-serve), and partner. Channels have very different economics.

**Blended CAC benchmarks:**
- PLG/self-serve SMB: $50–$500
- Inbound mid-market: $5K–$15K
- Enterprise outbound: $25K–$100K+

### LTV (Customer Lifetime Value)

```
LTV = ARPU × Gross Margin % / Logo Churn Rate

Or for cohort-based NPV calculation:
LTV = Σ (Monthly Gross Profit per customer) × (Retention Rate^month) / (1 + r)^month
where r = monthly discount rate
```

**LTV:CAC Ratio:**
- >3:1 = healthy SaaS economics (industry standard minimum)
- >5:1 = very efficient; potentially underinvesting in growth
- 1–2:1 = acquiring customers at near-loss; diagnose before scaling

### CAC Payback Period

```
CAC Payback (months) = CAC / (ARPU × Gross Margin %)
```

**Benchmarks:**
- PLG/SMB best-in-class: 6–12 months
- Mid-market: 12–18 months
- Enterprise: 18–36 months (common given long sales cycles)
- Aggressive growth: Payback >24 months OK if LTV is certain

### Burn Multiple

```
Burn Multiple = Net Cash Burn / Net New ARR

(Net burn = total spend minus all revenue)
```

Measures how efficiently the company is converting spend into ARR.

**Benchmarks:**
| Burn Multiple | Rating |
|--------------|--------|
| <0.5× | Exceptional |
| 0.5–1.0× | Excellent |
| 1.0–1.5× | Good (Series A) |
| 1.5–2.0× | Acceptable |
| 2.0–3.0× | Concerning; requires improvement plan |
| >3.0× | Unsustainable; immediate action required |

Andreessen Horowitz uses this as a primary efficiency metric for portfolio companies.

### Magic Number

```
Magic Number = Net New ARR (current quarter) / S&M Spend (prior quarter)
```

Measures S&M efficiency with a one-quarter lag to account for sales cycle.

**Benchmarks:**
- >1.0 = excellent ROI; step on the gas
- 0.75–1.0 = good
- 0.5–0.75 = adequate; optimize before scaling
- <0.5 = S&M inefficiency; diagnose before spending more

### Rule of 40

```
Rule of 40 = Revenue Growth Rate % + Profit Margin %

Use FCF margin (not EBITDA) for the profit measure.
```

Balances growth vs. profitability at scale. Below $10M ARR, growth dominates; apply Rule of 40 from Series B onward.

**Public SaaS benchmarks:**
- Top quartile: >50 (Datadog, Snowflake at peak)
- Strong: 40–50
- Median public SaaS: 35–45
- Below 30: Underperforming; likely trading at discount

**Premium multiples are paid for Rule of 40 >40:**
- ARR multiple premium correlation: companies at Rule of 40 >50 trade at 15–25× ARR
- Companies at Rule of 40 <30 trade at 4–8× ARR

---

## Financial Planning and Analysis (FP&A)

### Rolling Forecast (Best Practice)

Replace annual static budget with a **rolling 12-month forecast** updated monthly:

```
Format: Each month, extend the forecast one month forward
Benefits: Always 12 months of visibility; eliminates "game the budget" behavior;
          forces real-time business thinking

Monthly FP&A cycle:
  Week 1: Close prior month actuals in GL
  Week 2: Variance analysis (actual vs. forecast + vs. prior period)
  Week 3: Update rolling forecast with new assumptions
  Week 4: Board/leadership presentation

Variance analysis format:
  Revenue: Actual $X vs. Forecast $Y = +/- $Z (+/- W%)
  Root cause: New customer volume, ACV, timing, churn
  Actions: What changes in forward forecast?
```

### Headcount Planning

Headcount is typically 60–80% of total operating expenses for software companies. Plan at role level:

```python
# Headcount model structure
headcount_plan = {
    "engineering": {
        "current": 15,
        "Q1_adds": 3,
        "Q2_adds": 4,
        "Q3_adds": 5,
        "avg_fully_loaded_cost": 185000,  # salary + benefits + equity + tools
        "capacity_metric": "story_points_per_sprint",
    },
    "sales": {
        "current": 6,
        "Q1_adds": 2,
        "quota_per_rep": 600000,  # ARR quota
        "ramp_time_months": 6,    # Time to full productivity
        "ote_cost": 200000,       # On-Target Earnings (base + commission)
    }
}

# Sales capacity model
def model_sales_capacity(headcount_plan: dict) -> dict:
    reps = headcount_plan["sales"]["current"]
    quota = headcount_plan["sales"]["quota_per_rep"]
    ramp = headcount_plan["sales"]["ramp_time_months"]
    
    # New rep contributes at 50% capacity during ramp
    effective_capacity = sum([
        quota if months_tenured >= ramp else quota * 0.5
        for months_tenured in [... ]
    ])
    return {"theoretical_capacity": reps * quota, "effective_capacity": effective_capacity}
```

**Fully-loaded cost per employee (US):**
| Role | Base | Benefits + Taxes | Equity (diluted) | Total |
|------|------|-----------------|-----------------|-------|
| Senior Engineer | $160K | $50K | $40K | $250K |
| Account Executive | $90K base + $90K OTE | $55K | $25K | $260K |
| Product Manager | $150K | $50K | $35K | $235K |
| Customer Success | $80K | $40K | $15K | $135K |

### Zero-Based Budgeting (ZBB) for Efficiency

Instead of "last year + X%," justify every expense from zero:
1. List all activities and their costs
2. Rank by ROI/strategic importance
3. Allocate budget to highest-ROI activities first
4. Cut or defer low-ROI activities
5. Useful for restructuring periods or when Burn Multiple >2×

---

## Cash Flow Management

### Working Capital Optimization

**Days Sales Outstanding (DSO):**
```
DSO = (AR Balance / Revenue) × Days in Period

Lower is better. Industry benchmarks:
- Best-in-class SaaS: 25–35 days
- Average: 45–60 days
- Poor: >75 days (collections problem)
```

**Improvement levers:**
1. Enforce Net-30 payment terms with late payment interest (1.5%/month)
2. Offer 2/10 Net-30 discount (2% discount if paid within 10 days)
3. Annual prepay discount (10–15%) — customers pay upfront, massively improves DSO
4. Automate collections with dunning (automated reminder sequence)

**Days Payable Outstanding (DPO):**
```
DPO = (AP Balance / COGS) × Days in Period

Higher is better (you keep cash longer).
Extend vendor payment terms from Net-15 to Net-30 or Net-45.
```

**Negative Working Capital Advantage:** SaaS companies that collect annual prepay have negative working capital—customers fund operations. Deferred revenue = interest-free float.

### Cash Runway Calculation

```
Monthly Net Burn = Cash Out (all expenses) - Cash In (all revenue)
Runway = Cash Balance / Monthly Net Burn

Safety rule: Raise when runway = 12 months
  (fundraising takes 3–6 months; 12 months = 6 months cushion)

Target: Always maintain 18+ months of runway
```

**Runway extension without dilution:**
1. Annual prepay conversion: 10–15% discount for annual contracts converts monthly → annual cash
2. AWS/GCP/Azure startup credits: $25K–$500K available through startup programs
3. R&D tax credits: File R&D credits (Section 41) for immediate cash refund (startups with <$5M gross receipts can apply credits against payroll taxes)
4. Venture debt: 12–18 months of runway extension; 10–12% interest rate; 1–5% warrant coverage
5. Reduce burn: Defer non-essential hiring; convert contractors; renegotiate vendor terms

---

## Valuation

### SaaS Valuation Methods

**ARR Multiples (primary for growth-stage):**

ARR multiple = Enterprise Value / ARR

**Public SaaS multiples (2024–2025):**
| Growth Rate | NRR | Rule of 40 | ARR Multiple |
|------------|-----|------------|-------------|
| >40% | >120% | >50 | 15–25× |
| 30–40% | 110–120% | 40–50 | 10–15× |
| 20–30% | 100–110% | 30–40 | 6–10× |
| 10–20% | 90–100% | 20–30 | 3–6× |

**Private company benchmarks (Series A–C):**
- Early stage (<$5M ARR, high growth): 10–20× ARR
- Growth stage ($5M–$25M ARR): 8–15× ARR
- Scale stage ($25M+ ARR, >40% growth): 12–25× ARR

**Revenue multiple vs. NTM ARR:** Forward multiples (NTM = Next Twelve Months) are more relevant than LTM (Last Twelve Months). High-growth companies trade at NTM revenue multiples.

### DCF for Tech Companies

DCF is less relevant for pre-profit SaaS but essential for later-stage companies:

```
Terminal Value = FCF × (1 + g) / (WACC - g)
  where g = long-term growth rate (2–4%)
  WACC for tech: typically 10–15% (higher risk = higher WACC)

Enterprise Value = PV of 5-year FCF projections + PV of Terminal Value
Equity Value = Enterprise Value - Net Debt + Cash
```

**Key DCF assumption sensitivities for SaaS:**
- Terminal growth rate: ±1% changes EV by 30–40%
- WACC: ±2% changes EV by 25–35%
- Terminal year FCF margin: most critical assumption

### Comparable Company Analysis

```
Identify public peer set → Gather metrics → Calculate multiples → Apply to subject company

Key multiples:
EV/Revenue (NTM)    — for growth companies
EV/ARR              — SaaS-specific
EV/EBITDA           — for profitable companies
EV/FCF              — for cash-generative companies

Apply discount for:
- Private company illiquidity: 20–30% discount
- Size premium for large companies: 10–20% premium
```

---

## Revenue Recognition (ASC 606)

### Five-Step Model

1. **Identify the contract:** Written or oral agreement with commercial substance; collectibility probable
2. **Identify performance obligations:** Distinct goods or services (software license + support can be separable)
3. **Determine transaction price:** Total consideration including variable elements; apply constraint for variable consideration
4. **Allocate transaction price:** Based on standalone selling prices (SSPs)
5. **Recognize revenue when (or as) obligations are satisfied**

**Over time recognition triggers (any one):**
- Customer receives and consumes benefits as you perform
- Customer controls the asset as it's created (e.g., custom software development)
- Asset has no alternative use AND you have right to payment for work completed

**Point in time:** Delivery of software license, hardware, or completed deliverable.

### SaaS-Specific Recognition

**Subscription software:**
- Recognize ratably over contract period
- Annual contract = 1/12 per month
- Does NOT depend on when cash is collected

**Deferred revenue calculation:**
```
Jan 1: Customer pays $12,000 annual fee
Jan 31: Revenue recognized = $1,000
Balance sheet: Deferred Revenue = $11,000 (liability)
Dec 31: Revenue recognized = $1,000/month × 12 = $12,000 total
```

**Professional services:** Recognize based on % of completion (input or output method) or when deliverable is accepted.

**Usage-based pricing:** Recognize as usage occurs. Minimum commitments recognized ratably if customer has committed to minimum; variable usage above minimum recognized as consumed.

**Contract modifications:** May create new contract (separate SSP, recognize independently) or modify existing contract (catch-up adjustment or prospective change). Critical to document modification analysis.

### ASC 606 Practical Expedients for SaaS

- **Portfolio approach:** Apply ASC 606 to a group of similar contracts if not materially different from individual application
- **Costs to obtain a contract (ASC 340-40):** Sales commissions capitalize and amortize over expected contract life if average contract >12 months OR use practical expedient to expense if <12 months
- **Significant financing component:** Can ignore if <12 months between payment and delivery

---

## Capitalized Software (ASC 350-40)

### The Three Phases

| Phase | Accounting Treatment |
|-------|---------------------|
| **Preliminary project stage** (research, evaluation, feasibility) | Expense as incurred |
| **Application development stage** (coding, testing features for release) | **Capitalize as intangible asset** |
| **Post-implementation stage** (maintenance, minor bug fixes, training) | Expense as incurred |

**Capitalization criteria:**
- Technological feasibility has been established (preliminary phase complete)
- Company intends to complete, use, or sell the software
- Technical ability to complete
- Adequate resources exist to complete
- Future economic benefit exists

**Amortized over useful life** (typically 2–3 years for SaaS infrastructure; 3–5 years for core platform). Test for impairment annually.

**Practical impact:** Capitalizing development costs reduces current period operating expenses and increases assets. Investors adjust for this in comparables. SaaS companies capitalize 15–40% of engineering costs.

---

## Tax Strategy

### QSBS (Qualified Small Business Stock — Section 1202)

**Post-OBBBA 2025: Up to $15 million exclusion** from federal capital gains at exit.

**Requirements:**
- C-corporation (not LLC, S-corp)
- Active business (not financial services, hospitality, etc.)
- Aggregate gross assets ≤$75M when stock was issued
- Hold stock for **≥5 years**
- Original acquisition (not secondary purchase)
- Investors must be non-corporate

**Planning:** Early employees and founders who receive equity at incorporation (minimal FMV) and hold through a 5+ year exit can exclude $15M of gain per taxpayer per issuer. With spouse = $30M exclusion. Plan vesting/grants accordingly.

### R&D Tax Credits (Section 41)

**Federal:** 20% credit on "qualified research expenses" (QREs) above a base amount. Alternative Simplified Credit (ASC): 14% on 50% of QREs above 3-year average—simpler to calculate.

**Qualifying activities:**
- Software development for internal or commercial use
- Developing new algorithms, models, or architectures
- Testing and debugging (if developing new functionality, not maintenance)
- Exploratory AI/ML research

**2025 payroll offset (startups):** Companies with ≤$5M gross receipts and ≤5 years since first gross receipts can apply up to **$500,000/year** of R&D credits against employer payroll taxes (employer's share of FICA). This is cash in hand even before the company is profitable.

### Section 174 — R&D Deductibility (OBBBA 2025 Restoration)

**Pre-2022:** R&D costs expensed immediately when incurred (full deduction in year of expense).

**2022–2025 (TCJA change):** Domestic R&D must be capitalized and amortized over 5 years; foreign R&D over 15 years.

**OBBBA 2025 restoration:** The "One Big Beautiful Bill Act" (2025) restored **immediate expensing of domestic R&D costs** retroactively. Companies with 2022–2024 deferred R&D amortization should file amended returns.

**Impact:** Significant cash tax benefit for R&D-heavy AI companies. $10M in annual R&D spend = $10M immediate deduction vs. $2M/year over 5 years under the old rule.

### State Tax Nexus

Physical or economic nexus in a state triggers state income tax, sales tax, and payroll obligations:
- Employees working remotely create nexus in their state
- Economic nexus: Most states have $100K sales or 200 transactions threshold
- SaaS as a service: States differ on whether SaaS subscriptions are taxable (California: generally not; Texas: taxable)
- Use automated sales tax tools: Avalara, TaxJar, Vertex

### Transfer Pricing

For companies with international subsidiaries:
- Intercompany transactions must be at "arm's length" prices
- IP licensing to foreign subsidiaries: Must be at fair market value royalty rates
- Cost-sharing arrangements: Commonly used to shift IP ownership to low-tax jurisdictions (requires IRS documentation)
- Pillar 2 (Global Minimum Tax): 15% global minimum tax for companies with €750M+ revenue; most tech startups exempt but plan ahead

---

## AI Company-Specific Financials

### GPU Infrastructure Cost Model

**On-demand vs. reserved vs. spot pricing:**

| Option | Price | Best For |
|--------|-------|---------|
| AWS p4d.24xlarge on-demand (8×A100) | ~$32/hr | Development, burst |
| AWS reserved (1-year) | ~$18/hr | Steady workloads |
| CoreWeave H100 spot | $2–$4/hr/GPU | Training runs |
| Lambda Labs A100 | $1.75/hr/A100 | Budget training |
| On-prem (amortized over 3 years) | $0.50–$1.50/hr/A100 | High utilization |

**Training cost estimates:**
- 7B parameter model: $50K–$200K
- 70B parameter model: $1M–$5M
- 400B+ frontier: $10M–$100M+

**Capitalization decision:** Pre-production model training that will generate future economic benefit = capitalize under ASC 350-40. Experimental research runs = expense. Document the decision boundary.

### Inference COGS and Gross Margin Impact

AI inference is the largest COGS variable for LLM-powered products:

```python
# Inference cost model per customer
def calculate_inference_cogs(customers: int, avg_queries_per_day: int,
                               avg_input_tokens: int, avg_output_tokens: int,
                               model: str = "claude-sonnet-4-6") -> dict:
    
    COST_PER_MTok = {"claude-sonnet-4-6": {"input": 3.00, "output": 15.00},
                      "claude-haiku-4-5": {"input": 0.80, "output": 4.00},
                      "gpt-4o-mini": {"input": 0.15, "output": 0.60}}
    
    costs = COST_PER_MTok[model]
    monthly_queries = customers * avg_queries_per_day * 30
    
    monthly_cogs = monthly_queries * (
        avg_input_tokens * costs["input"] / 1_000_000 +
        avg_output_tokens * costs["output"] / 1_000_000
    )
    
    return {"monthly_inference_cogs": monthly_cogs,
            "cogs_per_query": monthly_cogs / monthly_queries,
            "annual_run_rate": monthly_cogs * 12}
```

**Gross margin benchmarks by AI business model:**
| Business Type | Gross Margin |
|--------------|-------------|
| Pure SaaS (minimal AI COGS) | 75–85% |
| AI-assisted SaaS (AI as feature, subscription pricing) | 60–72% |
| API wrapper / token pass-through | 30–50% |
| Foundation model inference API | 50–70% (with scale optimization) |

**Margin improvement levers:**
1. **Prompt caching:** Anthropic's 90% discount on repeated context tokens (cache hit rate typically 40–60%)
2. **Model selection:** Routing simple queries to cheaper models (Haiku vs. Sonnet vs. Opus)
3. **Output compression:** Shorter prompts through compression, summarization of context
4. **Batching:** Batch non-time-sensitive requests for throughput optimization (40–60% cost reduction)
5. **Distillation:** Train smaller, cheaper proprietary model on outputs of larger frontier model
6. **Caching outputs:** Semantic cache for frequently asked identical/similar questions (Upstash, Redis)

---

## Key Financial Metrics Dashboard

### Weekly Flash Report

```
Revenue:
  Current Month ARR: $X  (vs. $Y last month, $Z same month last year)
  MTD Net New ARR: $X (vs. $Y target)
  Churned MRR: $X  (annualized churn rate: X%)
  
Cash:
  Cash Balance: $X
  Monthly Net Burn: $X
  Runway: X months
  
Pipeline:
  Qualified Pipeline: $X  (at X× coverage ratio)
  Deals closed this month: X  (avg ACV: $X)
  
Team:
  Headcount: X (X engineers, X S&M, X G&A)
  Avg loaded cost/employee: $X
```

### Monthly Board Report

| Metric | This Month | Last Month | vs. Plan | YoY |
|--------|-----------|-----------|---------|-----|
| ARR | | | | |
| Net New ARR | | | | |
| NRR | | | | |
| Gross Margin % | | | | |
| Net Burn | | | | |
| Runway | | | | |
| Rule of 40 | | | | |
| Headcount | | | | |

---

## Common Financial Mistakes

1. **Confusing bookings, billings, and revenue:** Bookings = signed TCV; billings = invoiced; revenue = ASC 606 recognized. Track all three separately.

2. **Over-counting MRR from one-time revenue:** Professional services or implementation fees included in MRR inflate the metric artificially.

3. **Ignoring deferred revenue on balance sheet:** High deferred revenue is a positive indicator of customer commitment and cash health.

4. **Not building ARR by cohort:** Aggregate churn hides whether you're losing small or large customers.

5. **Undermodeling COGS growth for AI products:** Inference costs scale with usage; model your cost per unit before setting pricing.

6. **Incorrect ASC 606 treatment of usage-based contracts:** Usage-based billing recognized as consumed, not when billed.

7. **Missing R&D credit opportunity:** 20–40% of engineering spend often qualifies; payroll offset valuable for pre-revenue startups.

8. **Single banking relationship:** Post-SVB, maintain FDIC-insured accounts at 2+ banks; keep <$250K in any non-insured account; use money market funds for amounts above FDIC limits.
