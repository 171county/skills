# Vendor & Tool Management

## SaaS Stack Rationalization

### The Scale of the Problem (2025)
- Average organization: 275 SaaS apps in their portfolio
- 53% of SaaS licenses go unused
- Shadow IT accounts for 33.6% of total applications and 3.8% of SaaS spend
- Nearly 70% of CIOs list technology rationalization as a top initiative (Gartner, March 2025)
- Cost: ~$21M in wasted spend annually at mid-to-large organizations

**Root cause of bloat**: SaaS buying is distributed (teams buy on company credit cards without procurement review), contracts auto-renew, and tool consolidation never gets prioritized because it's urgent to no one until budget pressure hits.

### The Rationalization Framework

**Phase 1: Inventory (2-4 weeks)**
- Pull a complete list from: finance/AP (all SaaS invoices), IT (managed software), SSO provider (all authenticated applications), expense reports (individual subscriptions)
- Categorize by function: Communication, Project Management, Development, Analytics, Security, etc.
- Map to business owner (who owns this? who uses it?)

**Phase 2: Utilization Assessment (4-6 weeks)**
For each application:
- **License count** vs. **active users in last 30 days** (pull from admin consoles or SSOlogs)
- **Usage intensity**: Daily active users (DAU) vs. monthly active users (MAU)
- **Alternative coverage**: Does another tool we already own do this?
- **Contract terms**: Renewal date, auto-renewal clause, minimum commitment

**Phase 3: Rationalization Decision Matrix**

| Category | Action |
|----------|--------|
| >80% utilization, no overlap | Keep and optimize |
| >80% utilization, overlap exists | Keep — evaluate consolidation |
| 30-80% utilization, no overlap | Drive adoption or evaluate removal |
| <30% utilization | Cancel at next renewal unless critical |
| Shadow IT without procurement | Legalize (add to SSO) or block |

**Phase 4: Consolidation Targeting**
Consolidation generates the largest savings. Target areas of functional overlap:
- Multiple project management tools (Jira + Asana + Monday.com)
- Multiple communication channels (Slack + Teams + Discord)
- Multiple video conferencing tools (Zoom + Meet + Teams)
- Multiple documentation tools (Confluence + Notion + Google Docs)
- Multiple BI tools (Tableau + Looker + Metabase)

**Phase 5: Governance Going Forward**
- Establish a **SaaS procurement policy**: All new SaaS purchases above $X require approval from IT + Finance
- **30-day rule**: Before buying a new tool, check if existing tools cover the use case
- **Annual SaaS audit**: Review full inventory each fiscal year
- **SaaS management platform**: Zylo, Torii, BetterCloud, or Blissfully automate discovery, utilization tracking, and renewal alerts

---

## Vendor Assessment Framework

### The Weighted Scorecard
Use a scored evaluation for all significant vendor decisions. Standard weights:

| Criterion | Weight | Notes |
|-----------|--------|-------|
| Technical capability / fit | 30% | Does it solve the problem well? |
| Total cost of ownership (3-year) | 20% | License + implementation + integration + support + training |
| Implementation track record | 20% | References, case studies, time-to-value |
| Security and compliance posture | 15% | SOC 2 Type II, ISO 27001, GDPR, data residency |
| Vendor stability and longevity | 10% | Funding, customer base, product roadmap credibility |
| Integration ecosystem | 5% | Does it connect to your existing stack? |

**Anti-pattern**: Vendor selection as a political exercise where the decision is made before the scorecard is filled out. Require all evaluators to score independently before group discussion.

### Due Diligence Checklist
- [ ] Security certification (SOC 2 Type II, ISO 27001)
- [ ] Data processing agreement (DPA) reviewed by legal
- [ ] SLA terms: uptime guarantee, credits for downtime, response time commitments
- [ ] Data portability: can you export your data if you leave?
- [ ] Reference check: speak to 3 customers of similar size/use case
- [ ] Financial stability: funded runway, audited financials (for critical vendors)
- [ ] Roadmap review: does the product roadmap align with your future needs?

---

## Build vs. Buy vs. Partner

### The Decision Framework

**Buy when**:
- A mature commercial solution exists that solves 80%+ of your use case
- The capability is not a source of competitive differentiation
- Speed to market matters more than customization
- The vendor's continued investment in the product provides ongoing value
- Total cost of ownership (including implementation and integration) is lower than building

**Build when**:
- The capability is core to your competitive moat
- No commercial solution meets your requirements and won't for years
- You have the engineering capacity to build AND maintain it
- Data sensitivity or regulatory requirements prevent using external vendors
- The commercial options are significantly worse on cost at your scale

**Partner when**:
- You need speed (faster than build) and customization (more than buy)
- A technology partner has deep expertise you want access to without hiring
- The use case is important but not core enough to build in-house
- Revenue-sharing or joint go-to-market aligns incentives between you and the partner

### 2026 Context: Agentic AI Changes the Economics
AI agents are shifting the build/buy calculus. A task that previously required building or buying specialized software (e.g., a bespoke document processing pipeline) can now be handled by an AI agent built on top of a foundation model API. Before building or buying specialized software, ask: "Can an AI agent with existing tools solve 80% of this problem?"

### Evaluating Vendor Lock-in Risk
Rate lock-in risk on three dimensions:

1. **Data portability**: Can you export all your data in a standard format? (Critical) Can you do it programmatically via API?
2. **API dependency**: If this vendor's API becomes unavailable, what breaks? Build abstraction layers for critical API dependencies.
3. **Switching cost**: What is the realistic cost (time, money, disruption) of replacing this vendor in 2 years?

**Lock-in mitigation strategies**:
- Abstract vendor-specific code behind internal interfaces (adapter pattern)
- Maintain data exports in a vendor-neutral format
- Avoid vendor-proprietary query languages or data formats for critical data
- For critical infrastructure, maintain a "break glass" plan for rapid vendor replacement

---

## SLA Management

### What SLAs Must Include
Generic SLAs without specifics fail when you need them. A contract-worthy SLA includes:

1. **Availability metric**: Specific uptime percentage (99.9% = 8.7 hours downtime/year; 99.99% = 52 minutes/year)
2. **Measurement methodology**: How is uptime calculated? What counts as downtime? Who measures it?
3. **Reporting cadence**: Monthly uptime report provided by vendor
4. **Financial remedies**: Service credits for missed SLAs (typically 10-30% of monthly fee per hour of downtime beyond threshold)
5. **Escalation procedures**: Named contacts, response time commitments by severity tier
6. **Exclusions**: Planned maintenance windows, force majeure clauses
7. **Exit rights**: Can you exit the contract if SLAs are consistently missed?

### Defining Your Own SLOs/SLAs (Internal)
If you're providing services to customers or internal teams, define:
- **SLI** (Service Level Indicator): The actual measured metric (e.g., API response time p99)
- **SLO** (Service Level Objective): The target you aim to hit (e.g., p99 < 200ms)
- **SLA** (Service Level Agreement): The commitment with consequences (e.g., p99 < 200ms; if we miss this, customers get service credits)
- **Error budget**: The acceptable downtime within an SLO period. If SLO is 99.9% uptime per month, error budget = 43.8 minutes. Use error budget depletion as the trigger for engineering prioritization changes.

### API Dependency Risk Management
APIs are dependencies. Treat them like external risks:
- **Critical path APIs** (payment processing, authentication, cloud hosting): Require >99.99% SLA, have runbook for failure, test failover regularly
- **Important APIs** (CRM integration, analytics): Require >99.9% SLA, define graceful degradation behavior
- **Non-critical APIs** (enrichment, supplementary data): Accept >99.5% SLA, build with fail-open (service continues without the data)

**The Fallacies of Distributed Computing**: The network is unreliable, latency is not zero, bandwidth is finite, the network is not secure. Design every external API dependency assuming it will fail — have a timeout, a retry policy, a circuit breaker, and a fallback behavior.
