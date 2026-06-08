---
name: business-management-tech-expert
description: Expert-level business management advisor for technology companies, covering organizational design, engineering management, product management, OKR frameworks, DevOps/DORA metrics, SRE practices, incident management, data-driven decision making, FinOps, SaaS operations, customer success, board governance, and scaling operations from 1 to 1000+ employees. Provides authoritative, practitioner-grade frameworks calibrated for 2025-2026 tech company realities.
---

# Business Management Technology Expert

## Overview and Expert Mandate

Managing a technology company requires mastery across engineering leadership, product strategy, operational excellence, financial governance, and human systems — simultaneously. This skill provides expert-level guidance for technology company leaders at every stage. Every framework reflects current best practices as of 2025-2026, grounded in the realities of AI-augmented engineering, distributed teams, and the economic discipline required after the 2022-2024 tech correction.

---

## 1. Organizational Design

### The Core Frameworks

**Team Topologies (Skelton & Pais, 2019 — still the definitive model):**

Four team types:
1. **Stream-aligned teams:** Aligned to a business capability/user journey; primary flow of value delivery (80% of teams)
2. **Platform teams:** Provide internal services/products to reduce cognitive load for stream-aligned teams ("paved road")
3. **Enabling teams:** Temporary specialist teams that help stream-aligned teams acquire new capabilities
4. **Complicated subsystem teams:** Own complex systems requiring deep specialist knowledge (ML models, payment processing)

Three interaction modes:
1. **Collaboration:** Short-term, intensive working together to discover patterns
2. **X-as-a-Service:** One team consumes well-defined service from another with minimal interaction
3. **Facilitating:** Enabling team helps stream-aligned team develop capability

**Conway's Law implication:** Software architecture mirrors organizational communication structure. Design your org chart intentionally to produce the architecture you want.

### Organizational Evolution by Stage

**1-10 employees:** Flat structure; everyone does everything; CEO is de facto PM and engineering lead. No formal structure needed; create chaos if you impose it.

**10-50 employees:** Functional structure emerges (Engineering, Product, Sales, Operations). First management layer appears. Eng manager for teams >5. PM hired when product complexity warrants. 

**50-200 employees:** Product lines or customer segments drive structure. Product/Engineering/Design triads form. Platform team separates from stream-aligned teams. VP Engineering + CTO role clarification needed.

**200-1000 employees:** Business units or platforms become organizing units. Spotify model (squads/tribes/chapters/guilds) commonly adopted. Avoid pure functional organization at this scale — slows delivery.

**1000+ employees:** Full matrix or divisional structure. P&L accountability at division level. Corporate functions (Legal, Finance, IT, HR) distinct from business units.

### Spotify Model (Squads/Tribes/Chapters/Guilds)

**Squads:** Small autonomous teams (6-12) with end-to-end ownership of a feature area. Own product, engineering, and design. Long-lived. Minimize dependencies.

**Tribes:** Collection of squads working in related areas (up to ~150 people per Dunbar's Number). Have Tribe Lead; coordinate at tribe level.

**Chapters:** Horizontal group of specialists in same discipline across squads within a tribe (e.g., backend engineers in Payments tribe). Chapter Lead = line manager; ensures craft standards.

**Guilds:** Cross-tribe communities of interest (e.g., Security Guild, Accessibility Guild). Voluntary; share knowledge; set standards. No formal authority.

**Warning:** Spotify model is a reference architecture, not a template. Spotify has publicly admitted the model evolved significantly. Adapt to your context; don't cargo-cult.

---

## 2. Hiring Strategy for the AI Era

### Supply/Demand Landscape (2025)

- 1.6M open AI/ML positions in the US as of Q1 2025 (LinkedIn data)
- Only 518K candidates with relevant skills
- 3:1 demand-supply ratio creating sustained talent scarcity
- AI-augmented engineers (GitHub Copilot, Cursor, Claude) deliver 2-5x throughput increase
- Implication: Fewer but higher-quality engineers with AI augmentation > more engineers without AI fluency

### Hiring Philosophy

**Skills-based hiring** over credential-based:
- Drop degree requirements for most IC roles (Google, IBM, Delta have done this)
- Technical assessment based on real-world work samples, not abstract puzzles
- Portfolio review for engineers (GitHub, published projects)
- Structured interviews with defined rubrics to reduce bias

**Build vs. Buy vs. Borrow:**
- **Build:** Entry/mid-level roles with 6-18 month development runway; strong for culture fit + domain knowledge
- **Buy:** Senior/staff/principal roles requiring immediate impact; external hire
- **Borrow:** Fractional executives (CFO, CISO, CMO), contractors, staff augmentation for peak demand or specialized projects

### 2025-2026 Compensation Benchmarks (US, Tier 1 Markets)

| Role | Level | Base Salary | Total Comp (equity) |
|------|-------|-------------|---------------------|
| Software Engineer | L3/IC2 | $140-175K | $170-250K |
| Software Engineer | L4/IC3 | $175-220K | $220-380K |
| Senior SWE | L5/IC4 | $200-260K | $300-600K+ |
| Staff SWE | L6/IC5 | $240-320K | $500K-1M+ |
| Engineering Manager | L5-6 | $210-280K | $300-550K |
| Director of Engineering | L7 | $280-360K | $500K-900K |
| VP Engineering | — | $350-450K | $700K-1.5M |
| Product Manager | L4-5 | $160-210K | $200-350K |
| ML Engineer | L4-5 | $185-250K | $250-500K+ |

*Benchmarks reflect Bay Area/NYC/Seattle; 20-30% discount for other tier-1 US markets; 40-60% for remote-first hiring*

### Interview Process Anti-Patterns

Avoid:
- Leetcode grinding as primary filter (tests memorization, not engineering judgment)
- "Brilliant jerk" tolerance in any form
- Incomplete referencing (backdoor references are legal and effective)
- Moving offers without competitive urgency (top candidates have 2-5 competing offers)

### Onboarding Engineering

- **Day 1:** All systems access, buddy assigned, welcome kit
- **Week 1:** Codebase orientation, first PR merged (small documentation or bug fix)
- **Month 1:** First feature shipped independently
- **90-day check-in:** Structured review with manager; adjust expectations bidirectionally
- **Impact:** Structured onboarding reduces time-to-productivity from 6 months to 2-3 months

---

## 3. Engineering Management

### Dual-Track Career Ladder

Maintain separate IC and management ladders to avoid "accidental manager" problem:

**IC Track:**
- L1 Associate SWE: Executes well-defined tasks
- L2 SWE: Solves defined problems independently
- L3 Senior SWE: Handles ambiguous problems, mentors L1-L2
- L4 Staff SWE: Drives technical direction across teams, technical lead for major initiatives
- L5 Principal SWE: Company-wide technical strategy, recognized external expert
- L6 Distinguished/Fellow: Industry-defining contributions

**Management Track:**
- Engineering Manager: Direct people management of 5-8 engineers
- Senior EM / Group EM: Manages managers; owns technical roadmap for sub-org
- Director of Engineering: Business ownership of engineering for product line
- VP Engineering: Cross-functional leadership; org strategy; executive stakeholder
- SVP/EVP/CTO: Company-wide technical vision and culture

**Transition criteria:** Moving from IC to management requires genuine desire to grow through others' success — not just exhaustion of IC opportunities or desire for more authority.

### Tech Debt Management

**Four quadrant model:**
1. **Deliberate + Reckless:** "We don't have time for design" — should never accept
2. **Deliberate + Prudent:** "We must ship now, but know this creates debt" — acceptable with explicit tracking
3. **Inadvertent + Reckless:** "What's layering?" — result of poor hiring/culture
4. **Inadvertent + Prudent:** "Now we know what we should have done" — inevitable, handle with refactoring budget

**AI-generated code debt (2025 phenomenon):** LLM-generated code often lacks:
- Tests (AI generates code faster than tests)
- Meaningful abstractions (copy-paste at scale)
- Security considerations (LLMs reproduce training data patterns including vulnerabilities)
- Documentation of intent

Institute AI code review policies: All AI-generated code treated as "candidate code" requiring senior engineer review before merging. Require test coverage ≥80% regardless of source.

### Engineering Metrics (DORA — see Section 6 for DevOps detail)

Avoid vanity metrics (lines of code, commits/day, PRs merged). Focus on:
- **Deployment frequency:** How often code reaches production
- **Lead time for changes:** Commit to production time
- **Change failure rate:** % deployments causing incidents
- **Mean time to recovery:** Time to restore service after incident

---

## 4. Product Management

### Product Requirements Document (PRD) Standard

A PRD is a communication tool, not a specification. Elements:
1. **Problem statement:** What user problem are we solving? Who has it? How painful?
2. **Goals and success metrics:** What does success look like? What does failure look like?
3. **User stories / jobs-to-be-done:** Written as "When [situation], I want to [motivation] so I can [outcome]"
4. **Non-goals:** Explicit scope exclusions to prevent scope creep
5. **Open questions:** Unresolved ambiguities requiring decisions before build
6. **Dependencies:** Other teams/systems required
7. **Timeline:** Target ship date with milestone markers

Length: 1-3 pages for features; 5-10 pages for major initiatives. Avoid writing novels.

### Roadmap Types

| Type | Best For | Horizon | Risk |
|------|----------|---------|------|
| Feature roadmap | Sales collateral, engineering planning | 6-12 months | Locks team into solutions, not problems |
| Theme roadmap | Strategic communication | 12-18 months | Abstract; hard to execute against |
| Outcome/OKR roadmap | Product-led growth companies | 3-6 months | Requires mature discovery process |
| Now-Next-Later | Agile teams, startups | Rolling | May not satisfy enterprise sales |

**Recommendation:** Use Now-Next-Later internally (committed/planned/horizon); use theme roadmap externally with customers.

### Prioritization Frameworks

**RICE:**
- Reach × Impact × Confidence ÷ Effort
- Good for large backlog comparisons; requires calibrated estimates

**ICE:**
- Impact × Confidence × Ease
- Simpler; good for fast prioritization sessions

**Opportunity Scoring:**
- For discovery: Customer rates importance of outcome + satisfaction with current solution
- Opportunity score = Importance + max(Importance - Satisfaction, 0)
- Highest opportunity scores = underserved needs worth solving

**Priority anti-patterns:**
- HiPPO (Highest Paid Person's Opinion) driving roadmap
- Features as commitments to customers before validation
- Roadmap changes weekly (kills engineering velocity)
- Roadmap never changes (kills product relevance)

---

## 5. OKR Framework

### OKR Hierarchy

```
Company OKRs (quarterly, 3-5 objectives)
    └── Department OKRs (quarterly, 2-4 per department)
            └── Team OKRs (quarterly, 1-3 per team)
```

### OKR Quality Standards

**Objective characteristics:**
- Inspirational and directional (not tasks or projects)
- Achievable in the time period (not 5-year vision statements)
- Qualitative — does not contain numbers
- Clear enough that a new employee would understand

**Key Result characteristics:**
- Measurable (specific number and metric)
- Time-bound (tied to quarter end)
- Outcome-oriented (not activity/output)
- Achievable but ambitious (70% achievement = success, per Google standard)
- 2-5 KRs per Objective

**Example:**
```
Objective: Become the trusted data analytics partner for mid-market 
           manufacturing companies
  KR1: Increase Manufacturing segment NRR from 108% to 118%
  KR2: Achieve NPS ≥ 45 in Manufacturing segment (from 32)
  KR3: Land 3 Fortune 500 manufacturing logos
  KR4: Ship data pipeline feature cited as top-3 request by manufacturing 
       customers
```

### OKR Cadence

- **Annual OKRs:** Company-level; set strategic direction
- **Quarterly OKRs:** All levels; primary unit of execution
- **Weekly check-ins:** Progress updates, blocker surfacing (10-15 min, async-friendly)
- **Quarterly retrospective:** What drove high/low performance? What changes for next quarter?

### OKR Failure Modes

- **Task masquerading as KR:** "Complete redesign of onboarding flow" is a task, not an outcome
- **Sandbagging:** Setting KRs you know you'll hit at 100% eliminates stretch tension
- **OKR theater:** Leadership sets OKRs but decisions ignore them
- **Cascade rigidity:** Bottom-up OKR development is better than top-down cascade in most orgs
- **Too many OKRs:** 3-5 objectives, 2-5 KRs each max. More = diluted focus

---

## 6. DevOps and DORA Metrics

### CALMS Framework

**Culture:** Collaboration, learning from failure (blameless), experimentation
**Automation:** Automate everything repeatable
**Lean:** Small batch sizes, continuous flow, eliminate waste
**Measurement:** DORA metrics + business outcomes
**Sharing:** Transparency of metrics, post-mortems, runbooks

### DORA Metrics (2025 Benchmarks)

From DORA/Google State of DevOps Report 2024:

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Deployment Frequency | On-demand (multiple/day) | Weekly-monthly | Monthly-6 months | <6 months |
| Lead Time for Changes | <1 hour | 1 day-1 week | 1 week-1 month | >1 month |
| Change Failure Rate | 0-5% | 5-10% | 10-15% | >15% |
| Mean Time to Recovery | <1 hour | <1 day | 1 day-1 week | >1 week |

**Elite performer distribution (2024):** 18% of organizations surveyed. Elite performers are 2x more likely to meet business goals.

### CI/CD Pipeline Architecture

```
Developer commits → 
  PR lint + type check (< 2 min) →
  Unit tests (< 5 min) →
  Integration tests (< 15 min) →
  Security scan (SAST/DAST) →
  Build artifact + sign →
  Deploy to staging →
  Automated smoke tests →
  Deploy to production (canary 5%) →
  Monitor metrics 15 min →
  Progressive rollout to 100%
```

**Branching strategy:** Trunk-based development (short-lived branches, merge to main daily) for elite organizations. Feature flags enable incomplete features to be merged safely.

### Feature Flags

LaunchDarkly, Split.io, OpenFeature (open standard) — enable:
- Progressive rollouts (5% → 25% → 100%)
- A/B testing
- Kill switches for risky features
- Separation of deployment from release

**Technical debt:** Feature flags accumulate; require regular cleanup. Each flag should have an owner and expiry date.

---

## 7. Platform Engineering and Internal Developer Platform (IDP)

### Platform Engineering Principles

"Paved road" metaphor: Make the secure, compliant, observable path the path of least resistance.

**IDP components:**
- **Self-service portal:** Developers provision infrastructure, databases, services without tickets
- **Service catalog:** Approved services with templates (Backstage, Cortex, Port)
- **CI/CD platform:** Standardized pipeline templates
- **Observability stack:** Pre-configured logging, metrics, tracing (OpenTelemetry)
- **Secret management:** Vault, AWS Secrets Manager
- **Infrastructure-as-code templates:** Terraform/Pulumi modules for approved patterns

**Platform team anti-pattern:** Platform teams become bottlenecks by requiring tickets for every operation. Target: self-service for 80%+ of developer needs.

### Developer Experience (DX) Metrics

- **SPACE framework:** Satisfaction, Performance, Activity, Communication, Efficiency
- **Developer NPS:** Net Promoter Score specifically for developer experience
- **Time to first PR:** For new hires; measures onboarding efficiency
- **Toil ratio:** % of engineering time on manual, repetitive operational tasks (target <20%)

---

## 8. Site Reliability Engineering (SRE)

### SLI/SLO/SLA Framework

**Service Level Indicator (SLI):** A carefully defined quantitative measure of service behavior.
- Availability SLI: `(successful_requests / total_requests) * 100`
- Latency SLI: % requests served in under 200ms
- Error rate SLI: % requests returning 5xx

**Service Level Objective (SLO):** Target value for SLI.
- Availability SLO: 99.9% per rolling 30 days
- Latency SLO: 95% of requests < 200ms

**Service Level Agreement (SLA):** External commitment with consequences (credits, termination rights).
- Always more conservative than internal SLOs (e.g., SLO 99.9%, SLA 99.5%)

**Error Budget:** `Error budget = 100% - SLO`
- 99.9% SLO = 0.1% error budget = 43.8 min/month
- When error budget exhausted: halt new deployments; focus on reliability
- When budget healthy: invest in feature development

### Toil Management

**Toil definition (Google SRE):** Work directly tied to running a production service that:
- Is manual
- Is repetitive  
- Is automatable
- Scales with load (not value)
- Has no enduring value

**Toil cap:** Max 50% of SRE time; remaining 50% on engineering work that reduces future toil

### On-Call Practices

- **On-call rotations:** 1 week primary + 1 week secondary; maximum 2 people needed for any incident
- **Alert quality:** Alert fatigue is the enemy; every alert must be actionable
- **Runbooks:** Every alert must link to a runbook with diagnosis and remediation steps
- **Compensation:** On-call compensation required for non-exempt employees; consider on-call stipend for salary workers
- **Escalation path:** Clear escalation matrix from on-call engineer → senior engineer → engineering manager → VP

---

## 9. Incident Management

### Severity Classification

| Severity | Impact | Response | Resolution Target |
|----------|--------|----------|-------------------|
| P0/SEV1 | Complete outage; all users affected | Immediate (<5 min) | <4 hours |
| P1/SEV2 | Major functionality broken; 20%+ users affected | <15 min | <8 hours |
| P2/SEV3 | Significant degradation; workaround exists | <1 hour | <24 hours |
| P3/SEV4 | Minor issue; cosmetic or edge case | Next business day | 1 week |
| P4/SEV5 | Enhancement request / future improvement | Backlog | Sprint planning |

### Incident Response Process

**Declare:** Any engineer can declare an incident; err toward declaring
**Assign Incident Commander (IC):** Single point of coordination; does not debug
**Communicate:** Status page update within 15 min of P0/P1
**Triage:** Identify blast radius; roll back if faster than fix
**Resolve:** Restore service first; root cause later
**Post-mortem:** Blameless; within 5 business days of incident

### Blameless Post-Mortem

Five sections:
1. **Timeline:** Chronological events (discovery → impact → mitigation → resolution)
2. **Contributing factors:** System conditions that made incident possible (NOT who made a mistake)
3. **Impact:** User/business impact quantification
4. **What went well:** Don't only document failures
5. **Action items:** Specific, assigned, time-bound improvements

**Safety-II thinking:** Incidents occur in systems, not in individuals. Same person in different system conditions behaves differently. Fix the system.

---

## 10. Data-Driven Decision Making

### North Star Metric

Single metric that best captures the core value delivered to customers. Examples:
- Slack: Daily Active Users who send ≥1 message
- Airbnb: Nights booked
- Spotify: Time spent listening
- B2B SaaS: Weekly active accounts completing "core action"

**North Star selection criteria:**
- Measures value delivery (not revenue)
- Correlates with long-term retention and expansion
- Understandable by every employee
- Can be influenced by every team

### Input vs. Output Metrics

```
Output (lag): Revenue, Churn, NPS
       ↑
Input (lead): Feature adoption, time-to-value, support ticket volume
       ↑
Behavior: Session frequency, feature usage, integration count
```

Lead metrics predict output metrics with 2-4 week lag. Focus team execution on input metrics; measure success with output metrics.

### A/B Testing Standards

- **Minimum detectable effect:** Define before running test (what size improvement is worth detecting?)
- **Sample size:** Calculate required sample size for statistical power ≥80% at p<0.05
- **Duration:** Minimum 2 business weeks to capture day-of-week variation
- **Single change per test:** Never test multiple changes simultaneously (interaction effects)
- **Peeking problem:** Stopping tests early when results look good inflates false positive rate; use sequential testing methods or fixed horizon

**Modern data stack (2025):**
- Ingestion: Fivetran, Airbyte
- Warehouse: Snowflake, BigQuery, Databricks
- Transformation: dbt (SQL-based; ~90% market share for transformation layer)
- Visualization: Looker, Tableau, Metabase (open source)
- ML/AI: Feature store (Feast, Tecton) + model registry (MLflow, W&B)
- Experimentation: LaunchDarkly, Statsig, Optimizely

---

## 11. Remote and Distributed Team Management

### Async-First Culture

**Communication hierarchy:**
1. **Async first:** Document in writing; default to recorded over real-time
2. **Sync when necessary:** Decision points, emotional conversations, complex debugging
3. **Global time zone awareness:** Respect working hours; no "urgent" pings outside overlap

**Async communication tools:**
- Loom (video messages < 5 min)
- Notion/Confluence (documentation)
- Linear/Jira (work tracking)
- Slack/Teams (messages; expect response within 4-8 business hours, not instantly)

### Distributed Team Performance Management

**Weekly 1:1 structure (30-45 min):**
- Top of mind for employee: 15 min
- Manager updates/coaching: 10 min
- Career and growth: 10 min
- Skip level 1:1: Monthly, 30 min, with skip-level manager

**Performance review framework:**
- Avoid annual-only reviews; quarterly performance snapshots
- Separate compensation conversations from performance conversations
- Use calibration sessions to normalize ratings across managers
- Document performance concerns immediately in writing (for potential termination defense)

### Overlap Hours Requirement

Minimum viable overlap: 4 hours of common working hours per day for a team. Below 4 hours, synchronous collaboration degrades significantly. Cross-timezone teams may need to designate core overlap windows (e.g., 9 AM-1 PM PT for US/EU hybrid team).

---

## 12. Vendor Management and SaaS Sprawl

### The SaaS Sprawl Problem

Average enterprise wastes **$80.6M annually** on unused or underutilized SaaS (Productiv 2024 data). Typical 1,000-employee tech company has 250+ SaaS applications.

### Vendor Tiering

| Tier | Criteria | Management Approach |
|------|----------|---------------------|
| Tier 1 (Strategic) | Business-critical; hard to replace; >$250K/year | Executive sponsor; QBRs; formal governance |
| Tier 2 (Important) | Significant but replaceable; $50-250K/year | Annual reviews; vendor scorecard |
| Tier 3 (Commodity) | Easily replaceable; <$50K/year | Self-service; auto-renewal alerts |

**Vendor consolidation play:** At each contract renewal, evaluate consolidation opportunities. Fewer, deeper vendor relationships > many point solutions.

### SaaS Management Platforms (SMPs)

Tools: Torii, Zylo, Productiv, BetterCloud
Capabilities:
- Discover all SaaS from SSO + credit card + API integrations
- Measure adoption per seat (are users actually using it?)
- Automate license reclamation for inactive users
- Centralize renewal calendar
- Benchmark spend vs. peers

ROI: Typical SMP pays back in < 6 months through license reclamation and negotiation leverage.

---

## 13. FinOps: Cloud Cost Management

### FinOps Framework (Phases)

**Inform:** Visibility and allocation
- Tag all cloud resources: team, environment, product, cost center
- Allocate 100% of cloud spend to business unit (showback → chargeback)
- Unit economics: Cost per customer, cost per API call, cost per GB stored

**Optimize:** Optimize rates and usage
- **Commitment hierarchy:**
  - Spot/preemptible instances: 60-90% discount; for interruptible workloads (batch ML training)
  - Reserved instances / Savings Plans: 30-60% discount; for predictable baseline
  - On-demand: Full price; for variable peaks and new workloads
- **Right-sizing:** Identify over-provisioned instances; automate recommendations (AWS Compute Optimizer, GCP Recommender)
- **Waste elimination:** Idle resources, unattached volumes, unused load balancers

**Operate:** Continuous improvement culture
- Monthly FinOps review: Engineering teams present cost trends + initiatives
- Unit cost targets: Set budget for cost/unit; track weekly
- Anomaly alerting: Alert on cost spikes >20% week-over-week

### AI/ML Cost Management

New category requiring specialized FinOps:
- **Training costs:** Reserve GPU clusters; use spot for experimentation
- **Inference costs:** Token-level cost accounting; model caching (same prompts shouldn't re-infer)
- **Model selection:** Cost-per-quality tradeoff; use smaller models for simple tasks (Claude Haiku vs Opus)
- **Prompt optimization:** Fewer tokens = lower cost; compress system prompts; use structured outputs

---

## 14. SaaS Operations Metrics

### MRR Waterfall

```
Beginning MRR
  + New MRR (new logos this month)
  + Expansion MRR (upgrades, upsells, seat adds)
  - Contraction MRR (downgrades)
  - Churned MRR (cancellations)
  = Ending MRR
```

### Net Revenue Retention (NRR) Benchmarks

NRR = (Ending MRR from cohort / Beginning MRR from same cohort) × 100

| Category | Benchmark |
|----------|-----------|
| Elite (Snowflake/Datadog territory) | >130% |
| Excellent | 120-130% |
| Good | 110-120% |
| Acceptable | 100-110% |
| Struggling | <100% |

NRR >100% means revenue grows even with zero new customers. The single most important metric for SaaS company health.

### Churn Benchmarks (2025)

| Segment | Annual Logo Churn | MRR Churn |
|---------|------------------|-----------|
| Enterprise (>$50K ACV) | 3-8% | 2-5% |
| Mid-market ($5-50K ACV) | 10-20% | 6-12% |
| SMB (<$5K ACV) | 25-50% | 15-30% |

### Rule of 40

Revenue growth rate % + Profit margin % ≥ 40

Standard for evaluating SaaS business health. Elite companies exceed 60.

---

## 15. Customer Success

### CS Model by ACV

| ACV | CS Model | Ratio | Primary Tools |
|-----|----------|-------|---------------|
| >$100K | High-touch / Named CSM | 1 CSM: 5-10 accounts | QBRs, EBRs, custom success plans |
| $10-100K | Mid-touch / Pooled CSM | 1 CSM: 30-50 accounts | Automated health scoring + human escalation |
| <$10K | Low-touch / Tech-touch | 1 CSM: 200-500 accounts | In-app guidance, email sequences, community |

### Customer Health Scoring

Health score = weighted combination of:
- **Product usage:** DAU/WAU, core feature adoption, breadth of use
- **License utilization:** % seats active vs. purchased
- **Support health:** Open critical tickets, CSAT score
- **Engagement:** Response rate, exec sponsor engagement, NPS
- **Financial:** Invoices current, contract renewal proximity

Score: 0-100 scale; Red/Yellow/Green segmentation
- Green (70-100): Healthy; target for expansion
- Yellow (40-70): At risk; CSM outreach within 48 hours
- Red (0-40): High churn risk; immediate intervention + exec involvement

### Churn Prevention Playbook

**Early warning signals (30-90 days before renewal):**
- Usage drops >20% week-over-week for 2+ weeks
- Champion changes roles (title change in LinkedIn)
- Support escalations unresolved
- Missed QBRs / unresponsive to outreach
- Competitor evaluation discovered

**Intervention plays:**
1. Executive sponsor outreach (VP+ level)
2. Technical health check (identify and fix adoption friction)
3. Value realization review (document ROI achieved)
4. Contract flexibility (term extensions, pricing adjustments)
5. Escalation to product team for blocking issues

### Expansion Playbooks

- **Seat expansion:** Usage hitting license limits → natural trigger for upgrade conversation
- **Cross-sell:** Adjacent product serving same buyer → land and expand
- **Upsell:** Higher tier with more features → demonstrate unused value gap
- **Multi-year commitment:** 2-3 year deals for 10-15% discount; improves ARR predictability

---

## 16. Cloud Co-Sell and Technical Partnerships

### AWS Partner Network (APN)

**Co-sell motions:**
- List product on AWS Marketplace (accelerates procurement via customer's committed AWS spend)
- ACE (AWS Customer Engagement) program: Bi-directional deal sharing with AWS field
- ISV Accelerate: AWS-funded proof-of-concept funding, co-marketing, sales support

**AWS Marketplace benefits:**
- Customers use committed AWS spend (contracts already in procurement)
- Revenue share: AWS takes 3-20% depending on listing type
- Typical deal velocity improvement: 2-4x faster procurement

**Comparable programs:** Azure Marketplace + ISV Success Program (Microsoft), GCP Marketplace + ISV Program (Google), Salesforce AppExchange.

---

## 17. Board Governance

### Board Pack Structure

Monthly board update (2-4 pages):
1. Key highlights / company narrative (1 paragraph)
2. Top metrics dashboard (8-12 KPIs vs. plan)
3. P&L vs. budget
4. Headcount vs. plan
5. Key wins and pipeline
6. Risks and asks

Quarterly board meeting (2-3 hours):
- CEO update: Business narrative, key decisions
- Financial review: CFO presentation; full financial statements
- Deep-dive: One strategic topic (product, market, new initiative)
- Executive session: Board-only; no management

**Board pack timing:** Distribute 5-7 business days before meeting. Late board packs are a governance red flag.

### Board Composition Evolution

| Stage | Size | Composition |
|-------|------|-------------|
| Pre-seed | 1-3 | Founders |
| Seed | 3 | 2 founders + 1 investor or independent |
| Series A | 5 | 2 founders + 2 investors + 1 independent |
| Series B | 5-7 | 2 founders + 2-3 investors + 1-2 independent |
| Pre-IPO | 7-9 | Majority independent; diverse expertise |

---

## 18. Scaling Operations: Milestones and Inflection Points

### Key Scaling Inflection Points

**1 → 10 employees:**
- Founders do everything
- No formal process needed; move fast
- First non-engineer hire should be revenue (sales/marketing) or critical product gap
- Culture is "how founders behave, especially when no one is watching"

**10 → 50 employees:**
- First management layer; requires explicit management training
- Documentation becomes necessary (previously tribal knowledge)
- HR function needed (hiring, compensation, compliance)
- Finance: Controller or outsourced CFO

**50 → 200 employees:**
- Middle management appears; founders lose direct line to everyone
- Company values must be explicit and operationalized
- Department OKRs needed; strategy cascades are not automatic
- Specialist functions: Legal (fractional GC), FP&A, Customer Success
- Systems: HRIS, ATS, advanced CRM

**200 → 1,000 employees:**
- VP-level leadership team; CEO no longer "runs the business" day-to-day
- Business units or product lines as organizing principle
- Full-time GC, CFO, CHRO, CMO
- Process architecture matters: Too little = chaos; too much = bureaucracy
- M&A activity possible

**1,000+ employees:**
- Division P&L accountability
- Formal planning cycles (annual plan + quarterly forecasts + monthly closes)
- Corporate functions distinct from business units
- Governance: Audit committee, compensation committee

---

## 19. Compliance and Regulatory Management

### SOC 2 Type II Roadmap

**Phase 1 (0-3 months):** Readiness assessment
- Gap analysis vs. AICPA Trust Services Criteria
- Security controls implementation
- Policy documentation (>40 policies needed)
- Vendor risk management program

**Phase 2 (3-6 months):** Observation period
- Auditor selected (Drata, Vanta, Secureframe automate evidence collection)
- 6-month observation window
- Evidence collection for each control
- Access reviews, vulnerability scans, penetration test

**Phase 3 (6-9 months):** Audit and report
- Auditor testing and review
- Issue resolution
- SOC 2 Type II report issued

**Cost:** $30-80K first year; $20-50K annually for surveillance audits. Automation platforms (Vanta, Drata) reduce manual effort by 60-70%.

### ISO 27001:2022

Latest revision (October 2022) added 11 new controls including:
- Threat intelligence
- Information security for cloud services
- ICT readiness for business continuity
- Physical security monitoring
- Configuration management
- Information deletion
- Data masking

**ISO 27001 vs. SOC 2:**
- SOC 2: US-centric; required by US enterprise customers
- ISO 27001: International; required for EU enterprise sales + some regulated industries
- Many companies pursue both; significant overlap in controls

### EU AI Act Compliance

(See Business Tech Law Expert skill for full detail on EU AI Act)

Key compliance program elements:
- AI system inventory and risk classification
- Technical documentation for high-risk systems
- Conformity assessment procedures
- Post-market monitoring
- Incident reporting system

---

## 20. Risk Management

### NIST Cybersecurity Framework (CSF) 2.0 (2024)

Six core functions (expanded from five in CSF 1.1):
1. **Govern:** Establish risk governance, strategy, supply chain risk management
2. **Identify:** Asset management, risk assessment, improvement planning
3. **Protect:** Access control, awareness/training, data security, platform security
4. **Detect:** Continuous monitoring, adverse event analysis
5. **Respond:** Incident management, communication, analysis, mitigation
6. **Recover:** Incident recovery, communication

### Risk Register

Maintain dynamic risk register with:
- Risk description
- Likelihood (1-5 scale)
- Impact (1-5 scale)
- Risk score (Likelihood × Impact)
- Owner
- Mitigation plan
- Residual risk after mitigation

Review quarterly; escalate high-score risks (>15) to board.

### Business Continuity / Disaster Recovery

**Recovery objectives:**
- **RTO (Recovery Time Objective):** Maximum acceptable downtime before service restored
- **RPO (Recovery Point Objective):** Maximum acceptable data loss

| Tier | RTO | RPO | Strategy |
|------|-----|-----|---------|
| Tier 0 (Mission-critical) | 0-15 min | 0-5 min | Active-active multi-region |
| Tier 1 (Business-critical) | 1-4 hours | 15-60 min | Active-passive hot standby |
| Tier 2 (Important) | 4-24 hours | 1-4 hours | Warm standby |
| Tier 3 (Non-critical) | 24-72 hours | 24 hours | Backup restore |

Test DR plan annually; tabletop exercise semi-annually.

### Cyber Insurance

Coverage components:
- First-party: Breach response costs, ransomware, business interruption
- Third-party: Regulatory fines, litigation defense, customer notification

**2025 cyber insurance market:** Premium increases moderating after 2021-2022 spike. MFA implementation often required for coverage. Pre-requisites: EDR deployment, MFA on all remote access, regular patching, tested backups, incident response plan.

---

## Quick Reference: Management Frameworks by Situation

| Situation | Framework |
|-----------|-----------|
| Team structure design | Team Topologies (stream-aligned, platform, enabling, complicated subsystem) |
| Prioritizing features | RICE or Now-Next-Later |
| Goal-setting | OKRs (3-5 objectives, 2-5 KRs each, 70% achievement = success) |
| Measuring engineering delivery | DORA four metrics |
| Managing service reliability | SLI/SLO/Error Budget |
| Running incident response | Declare → IC → Status Page → Triage → Resolve → Post-mortem |
| Controlling cloud costs | FinOps: Inform → Optimize → Operate |
| Managing customer risk | Health scoring: Red/Yellow/Green |
| Scaling organization | Milestones: 1-10 / 10-50 / 50-200 / 200-1000 / 1000+ |
| Security compliance | SOC 2 Type II → ISO 27001 → EU AI Act |
| Board communication | Monthly update + Quarterly board meeting + Board pack 5-7 days prior |
