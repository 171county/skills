# Metrics, Dashboards & Data Culture

## The North Star Metric Framework

### What a North Star Metric Is
The North Star Metric (NSM) is the single metric that best captures the core value your product delivers to customers. It is a leading indicator of long-term sustainable growth. When the NSM goes up, you're growing in a healthy, durable way. When it goes down, something fundamental is wrong.

**North Star Metric examples from tech companies**:
| Company | North Star Metric | Why |
|---------|-------------------|-----|
| Spotify | Time spent listening | Captures music engagement; predicts subscriber retention |
| Airbnb | Nights booked | Captures marketplace health for both hosts and guests |
| Slack | Messages sent per team | Captures actual team activation, not just sign-ups |
| Facebook | Daily Active Users (DAU) | Usage frequency, not just account creation |
| Netflix | Total watch time | Engagement depth predicts retention |
| Stripe | Total payment volume | Aligns Stripe's revenue with customer success |
| GitHub | Daily active developers | Usage by core audience predicts expansion |

### Criteria for a Good NSM
- **Measures value delivered**, not activity or vanity (registered users, app downloads)
- **Predicts long-term revenue** (leading indicator, not lagging)
- **Is actionable** — teams can identify levers that move it
- **Is measurable** with current tooling
- **Is aligned** with company mission, not just financials
- **Is singular** — one NSM, not three

**Anti-pattern NSMs**:
- Revenue (lagging indicator; tells you what already happened)
- Registered users (measures signup, not value)
- Page views (measures traffic, not engagement)
- Number of features shipped (measures activity, not outcome)

### The Input Metrics Tree
The NSM is a scoreboard. To move the scoreboard, identify the **input metrics** (also called "lever metrics") that directly influence it:

```
North Star Metric: Nights booked (Airbnb)
├── Supply inputs
│   ├── New hosts acquired per month
│   ├── Active listing rate (% of listings with at least 1 booking in 30 days)
│   └── Average nights available per listing
└── Demand inputs
    ├── Qualified visitor volume (traffic × search intent)
    ├── Search-to-booking conversion rate
    └── Return guest rate
```

Each input metric maps to a team or function. Every team's OKRs should connect to one or more input metrics. The NSM is the single shared scoreboard.

### The Metrics Hierarchy
```
Level 1: North Star Metric (one per business)
Level 2: Functional KPIs (one per major function: Growth, Revenue, Ops, Engineering)
Level 3: Team metrics (per squad; inform but don't replace KPIs)
Level 4: Diagnostic/operational metrics (tracked but not reported up the chain)
```

Never report Level 4 metrics to executives. Never incentivize teams on Level 1 metrics they don't control.

---

## Leading vs. Lagging Indicators

| Type | Definition | Example | Timeliness |
|------|-----------|---------|------------|
| **Lagging** | Outcome of past activity; what already happened | Revenue, churn rate, NPS | Weeks-months delay |
| **Leading** | Predicts future outcomes; what is happening now | Activation rate, engagement score, pipeline coverage | Days-weeks |
| **Coincident** | Moves in real-time with the phenomenon | Active users today, API calls/sec | Real-time |

**The management principle**: Manage by leading indicators; evaluate results by lagging indicators. If you only look at lagging indicators (revenue, churn), you're always managing in the past.

**Paired metrics**: For every lagging metric, identify its 1-2 leading predictors:
- Revenue (lagging) → Pipeline ARR × close rate (leading)
- Churn (lagging) → Feature adoption rate + support ticket volume (leading)
- Time-to-hire (lagging) → Application velocity + interview-to-offer rate (leading)

---

## Vanity Metrics: What to Avoid

**Vanity metrics** look impressive but don't correlate with actual business outcomes or decision-making:

| Vanity Metric | Why It's Misleading | Better Alternative |
|--------------|--------------------|--------------------|
| Total registered users | Includes inactive accounts; doesn't measure value | Monthly/Daily Active Users |
| App downloads | Uninstalled apps count as downloads | 7-day retention rate |
| Page views | One user refreshing 100 times = 100 views | Unique engaged sessions |
| Impressions | Seen ≠ read ≠ acted on | Click-through rate × conversion |
| Emails sent | Output metric; doesn't reflect outcome | Email → qualified demo conversion |
| PRs merged | Measures activity, not value | Features adopted by users |

**The test**: Can you make a decision based on this metric that changes how you allocate resources? If no, it's vanity.

---

## Data-Informed vs. Data-Driven Culture

### The Critical Distinction
- **Data-driven**: Data decides. Metrics determine outcomes. Human judgment is replaced by data.
- **Data-informed**: Data informs. Humans use data as evidence in judgment calls. Data is consulted, not deferred to.

**Why data-informed is correct**: Data tells you what happened; it doesn't tell you why or what to do next. Data can be misleading (survivorship bias, Goodhart's Law — "when a measure becomes a target, it ceases to be a good measure"). Expert judgment, contextual knowledge, and ethical reasoning must inform data interpretation.

**Goodhart's Law in practice**: If you optimize engineering teams purely for DORA metrics, you get frequent but trivial deployments, faster lead time on small PRs while large architectural work stalls, and gaming of the metrics. Use metrics as diagnostics, not as performance scorecards.

### Building a Data-Informed Culture
1. **Data accessibility**: All team members can access the dashboards relevant to their work — not just analysts
2. **Metric literacy**: Teams understand what their metrics mean, how they're calculated, and what could make them misleading
3. **Decision documentation**: When a decision is made using data, document the data that was considered and the reasoning
4. **Metric review cadence**: Leadership reviews the key metrics weekly; the meaning of movements is discussed, not just the numbers
5. **Explicit challenge norms**: Anyone can question a metric or an interpretation — data literacy and psychological safety are linked

**The cultural bottleneck**: Most BI tool implementations fail not because of technology but because teams don't adopt standards, maintain data quality, or use dashboards for actual decisions. The stack evolves; the culture doesn't.

---

## The Modern BI Stack

### Architecture Overview (2025-2026 Standard)

```
Ingestion → Storage → Transformation → Semantic Layer → Visualization → AI Layer
```

| Layer | Purpose | Tool Options |
|-------|---------|-------------|
| **Ingestion** | Move data from source systems to warehouse | Fivetran, Airbyte, Stitch |
| **Storage** | The data warehouse or lakehouse | Snowflake, BigQuery, Databricks, Redshift |
| **Transformation** | SQL-based data modeling | dbt (primary standard), SQLMesh |
| **Orchestration** | Schedule and monitor data pipelines | Airflow, Prefect, Dagster |
| **Semantic Layer** | Consistent metric definitions across tools | Looker (LookML), dbt Metrics, Cube |
| **Visualization / BI** | Dashboards and reports | Metabase, Looker, Tableau, Power BI |
| **Governance** | Data catalog, lineage, quality | Atlan, DataHub, Monte Carlo |
| **AI Layer** | AI-powered analytics, NL querying | Databricks AI/BI, Vertex AI, Cortex |

### Tool Selection by Maturity Stage

**Startup / Early Stage (< $10M ARR, <50 employees)**
- Use Metabase. Open-source, deployable in under an hour on your existing data. Fast to value. Handles 80% of dashboarding needs a growth-stage company has.
- Pair with dbt for data transformation from the start — building dbt habits early prevents painful SQL sprawl later.

**Growth Stage (Series B-C, 50-500 employees)**
- Metabase + dbt + BigQuery/Snowflake is the standard stack. Add Fivetran or Airbyte for systematic data ingestion.
- Consider Looker if you have dedicated analytics engineering resources and need a governed semantic layer (LookML ensures consistent metric definitions across all reports).
- Looker pricing starts in the high five figures annually — budget accordingly.

**Scale (Series D+, enterprise)**
- Full modern data stack: Fivetran → Snowflake/BigQuery → dbt → Looker/Tableau → Atlan/DataHub for governance
- Invest in data engineering headcount before tool investment — tooling without people to maintain it creates data swamps

### dbt: The Standard for Transformation
dbt (Data Build Tool) has become the standard for SQL-based data transformation. Key capabilities:
- **Modular SQL**: Build reusable data models (like functions for data)
- **Testing**: Define tests on your data (row count, uniqueness, not-null, referential integrity)
- **Documentation**: Auto-generate data documentation from model metadata
- **Lineage**: Visual DAG showing how data flows from source to report
- **Version control**: dbt models live in git — data transformations are code

**The ELT shift** (Extract-Load-Transform vs. ETL): dbt enabled the shift from transforming data before loading to loading raw data first, then transforming in the warehouse. This is now the standard because warehouse compute is cheap, and loading raw data first preserves all original information.

### The Semantic Layer Problem
Without a semantic layer, "revenue" means 5 different things in 5 different dashboards because 5 analysts wrote 5 different SQL queries. The semantic layer (Looker's LookML, dbt Metrics, Cube) defines metrics once and reuses them everywhere. Solving this is the highest-leverage data engineering investment for companies with >5 analysts.

---

## Dashboard Design Principles

### The Three Dashboard Types

**1. Strategic Dashboards** (for executives, weekly/monthly)
- 5-10 metrics maximum
- Trend lines over time (not just current value)
- Comparison to target and prior period
- Traffic-light status (green/amber/red)
- Automated commentary for significant movements

**2. Operational Dashboards** (for teams, daily)
- Real-time or near-real-time data
- Actionable metrics only — if a metric being red doesn't change what someone does today, it doesn't belong
- Alert-driven: anomalies surface automatically, not through dashboard review
- Owned by the team — they understand what each metric means and what to do when it moves

**3. Analytical Dashboards** (for decision-making, ad hoc)
- Deep exploration capabilities
- Filtering and drill-down
- Comparative analyses
- Owned by analysts; used by PMs and leadership for specific decisions

### Dashboard Anti-Patterns
- **Too many metrics**: A dashboard with 40 metrics signals nothing. 5-10 is the target.
- **No targets**: A metric without a target is just a number. Show the target alongside the actual.
- **Stale data**: A dashboard that stakeholders distrust because the data is unreliable is worse than no dashboard. Invest in data quality before visualization.
- **Metrics that aren't decisions**: Every metric on a strategic dashboard should map to at least one decision that leadership could make if it moves.
