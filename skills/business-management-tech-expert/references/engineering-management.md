# Engineering Management

## DORA Metrics: The Gold Standard for Engineering Health

DORA (DevOps Research and Assessment) metrics are the most validated framework for measuring software delivery performance. Originally four metrics; DORA's 2023 report added a fifth.

### The Five DORA Metrics

**1. Deployment Frequency**
How often code is deployed to production. Measures the cadence of value delivery.
- Elite (top 15%): Multiple deploys per day
- High: Between once per day and once per week
- Medium: Between once per week and once per month
- Low: Between once per month and once every six months

**2. Change Lead Time**
Time from code commit to running in production. Measures the efficiency of the delivery pipeline.
- Elite: Less than one hour
- High: Between one day and one week
- Medium: Between one week and one month
- Low: Between one month and six months

**3. Change Failure Rate**
Percentage of deployments that cause a production incident or require a hotfix.
- Elite: Less than 4%
- High: 4-15%
- Medium/Low: 16-45%+

**4. Failed Deployment Recovery Time (FDRT) / MTTR**
Time to recover from a production failure. DORA's 2023 report preferred FDRT as it specifically measures engineering-owned failures (not infrastructure failures). MTTR (Mean Time to Recovery) is still the widely used term.
- Elite: Less than one hour
- High: Less than one day
- Medium: Between one day and one week
- Low: More than one week

**5. Reliability (2023 addition)**
Whether services meet their defined SLOs (Service Level Objectives). Captures operational stability independent of deployment events.

**2025 DORA context**: DORA's 2025 focus was entirely on AI-assisted development. Key finding: AI coding tools increase deployment throughput (more code, more deploys) but also increase delivery instability (higher change failure rates) unless paired with robust testing and code review practices. Elite teams using AI tools invest disproportionately in automated testing to compensate.

**The fifth metric (rework rate)**: Some practitioners track rework rate — percentage of deployments that are unplanned fixes for user-facing bugs. This is distinct from CFR (which captures outages) and reveals technical debt and quality leakage that MTTR doesn't surface.

### Measurement Setup
- Instrument your CI/CD pipeline (GitHub Actions, GitLab CI, Buildkite, CircleCI)
- Tools: LinearB, Sleuth, Faros, Swarmia, DX, Jellyfish, Waydev
- Start with deployment frequency and lead time — they're easiest to instrument and most actionable
- CFR requires tagging deployments that caused incidents — requires incident management tooling integration (PagerDuty, Opsgenie)

### DORA Anti-patterns
- Using DORA to compare teams against each other (gaming ensues)
- Optimizing one metric in isolation (deploy frequency up → failure rate up if quality drops)
- Treating "elite" as the target for every team (a team maintaining legacy infrastructure may correctly operate at "medium" levels)

---

## Beyond DORA: The SPACE Framework and DX Core 4

DORA measures delivery pipeline health but misses developer experience, cognitive satisfaction, and business impact.

### SPACE Framework (GitHub/Microsoft Research, 2021)
Five dimensions of developer productivity:

- **S — Satisfaction and well-being**: Developer satisfaction surveys, burnout indicators. Cannot be inferred from output metrics alone.
- **P — Performance**: Quality and reliability of outcomes. Defect rates, customer satisfaction, error rates.
- **A — Activity**: Engineering system events. PRs opened, code reviews completed, deployments. Use as context, never as the primary measure.
- **C — Communication and Collaboration**: How well teams share knowledge. PR review turnaround time, documentation quality, meeting load.
- **E — Efficiency and Flow**: Ability to do work with minimal friction. Flow time (time from PR open to merge), interruption rate, toil percentage.

**Key SPACE insight**: No single metric captures productivity. Always measure across at least 3 dimensions. Developer surveys (for S and E) are mandatory — you cannot infer developer experience from systems data alone.

### DX Core 4 (2024)
Synthesizes DORA, SPACE, and DevEx into four dimensions: Speed, Effectiveness, Quality, and Impact. The framework is designed for executive reporting — one metric per dimension, easily tracked.

- **Speed**: Deployment frequency, change lead time
- **Effectiveness**: Developer satisfaction score (DSAT), percentage of time in flow state
- **Quality**: Change failure rate, escaped defects
- **Impact**: Feature adoption rate, business value delivered per sprint

### Flow Metrics
- **Flow Time**: From feature approval to production. Full value stream measurement.
- **Cycle Time**: Active development time (from first commit to deploy). Subset of flow time.
- **Flow Efficiency**: (Active time / Total flow time) × 100. Industry average: 15-25%. World-class: 40%+.
- **Throughput**: Items delivered per time period (normalize by complexity category, not story points).

---

## Technical Debt Management

### The Cost
McKinsey research: systematic technical debt consumes 20-40% of development capacity before it's addressed. By 2025, nearly 40% of IT budgets consumed by debt maintenance at organizations that don't actively manage it.

### Classification
Not all technical debt is equal. Classify before deciding to pay down:

| Type | Description | Priority |
|------|-------------|----------|
| Critical | Security vulnerabilities, compliance gaps | Immediate — non-negotiable |
| High | Performance degradation affecting users, prevents scaling | Address within 1-2 quarters |
| Medium | Code maintainability issues, missing tests, outdated dependencies | Plan in regular sprints |
| Low | Code style, minor refactors, documentation gaps | Address when adjacent code is touched |

### The Debt Register
Maintain a living technical debt register (in your project management tool or a dedicated wiki page) with:
- Description of the debt
- Classification (critical/high/medium/low)
- Estimated remediation cost (engineer-weeks)
- Business impact if unaddressed (risk to delivery velocity, system stability, security posture)
- Owner (team responsible for paying it down)

### Allocation Strategy
- Dedicate **10-20% of every sprint** to technical debt reduction. Not "when we have time" — scheduled alongside features.
- "**Debt sprints**" (entire sprint dedicated to debt) are a trap — they create the illusion of progress but don't change the rate at which debt accumulates.
- The **Boy Scout Rule**: leave code cleaner than you found it. Enforce in PR review culture.
- Refactoring and new feature delivery are not mutually exclusive. Strangle pattern (incrementally replace legacy components rather than big-bang rewrites) is the safest approach to large debt.

---

## Incident Management & On-Call Culture

### The Blameless Postmortem (Google SRE Standard)
After every significant incident, conduct a postmortem that:
1. **Describes what happened** — timeline of events, detection to resolution
2. **Identifies contributing factors** — systemic causes (not individuals) using 5 Whys. The goal: "what system condition allowed this failure?" not "who caused this?"
3. **Notes what went well** — explicitly reinforce behaviors that limited impact
4. **Generates action items** — each item is specific, owned by a named person, and due-dated. Separate mitigative actions (fix this specific gap) from preventative actions (fix the class of failure).

**Blameless culture is non-negotiable**: Teams that fear blame will hide information. Hidden information makes incidents worse and prevents systemic improvement. Psychological safety is the prerequisite for honest postmortems.

### On-Call Culture Principles
- **On-call is a shared burden** — rotate across the full team, including senior engineers and managers
- **On-call requires preparation** — no one should go on-call without runbooks, documented escalation paths, and access to all required systems
- **Runbooks vs. Playbooks**: A playbook is the high-level strategy for handling a category of incident (database outage, authentication failure). A runbook is the specific step-by-step commands for a specific scenario. Both are mandatory.
- **On-call load must be measured**: Track alert volume, pages per shift, time-to-resolve. Alert fatigue (>5 actionable pages per on-call shift) is a system health indicator, not a staffing problem to solve by adding headcount.
- **Automate alert remediation** before expanding alert scope: If the same alert fires weekly and requires the same fix, automate the fix.

### Incident Severity Tiers
Standardize incident severity so responders know what response is expected:

| Severity | Definition | Response SLA |
|----------|------------|--------------|
| P0/SEV1 | Complete product outage or data loss | Wake anyone up; 15-minute response |
| P1/SEV2 | Major feature unavailable; >20% of users affected | 30-minute response during business hours |
| P2/SEV3 | Degraded performance or minor feature down | Next business day |
| P3/SEV4 | Minor bugs, cosmetic issues | Planned sprint |

### Tooling
- **Alerting & On-Call**: PagerDuty, Opsgenie, incident.io, Rootly
- **Observability**: Datadog, Grafana + Prometheus, New Relic, Honeycomb (for distributed tracing)
- **Incident Coordination**: Slack integration with automated war-room creation, Statuspage for customer communication

---

## Engineering Hiring & Performance Management

### Leveling Frameworks
Most tech companies use 6-7 levels: L1/SWE I through L6-L7/Principal or Distinguished Engineer. Key design principles:

- **IC track must parallel the management track**: Staff Engineer ≈ Engineering Manager in scope and compensation. Forcing engineers to manage to advance is the #1 cause of losing great technical talent.
- **Levels describe demonstrated performance**, not tenure. Promotion criteria must be explicitly written and consistently calibrated.
- **Impact scope scales with level**: Senior = owns a service. Staff = owns how multiple services work together, including deploy story, telemetry, and deprecation plans. Principal = owns an entire domain or drives cross-org technical strategy.

### Promotion Process
- Engineers should operate at the next level for **6-12 months before promotion** — calibration committees promote demonstrated capability, not potential.
- **Promotion criteria must separate technical work from influence**: A strong L5 technical packet is necessary but not sufficient for L6. At Staff+, written artifacts (design docs, RFCs, postmortems), mentoring, and cross-team influence matter as much as code.
- **Calibration committees** reduce manager bias and ensure consistency across the org.

### Performance Management
- **OKRs for teams, not individuals** for goal-setting. Individual performance assessment uses the level rubric, not OKR grades.
- **Regular 1:1s** (weekly) as the primary performance management tool — not annual reviews.
- **PIPs (Performance Improvement Plans)** should be preceded by 3-6 months of documented coaching. PIPs that appear without context signal a management failure, not an employee failure.

### Compensation Benchmarking
- **Levels.fyi**: Best source for public tech company compensation by level. Self-reported but directionally accurate, especially for engineering roles at established tech companies.
- **Radford (Aon) and Mercer**: The institutional compensation surveys used by large tech companies. Require subscription. More rigorous methodology, less real-time than Levels.fyi.
- **Benchmark practice**: Target 50th percentile of market for base salary; use equity and bonus to compete above market for high performers. Adjust target percentile based on company stage — early-stage startups must compete on equity upside.
- **Equity refresh grants**: Refresh grants are additional equity grants given to retain employees after initial grants approach full vesting. 2025 trend: moving from 100% time-based vesting to hybrid performance/time-based for refresh grants. Refresh grant sizes have compressed: 2023 — 48% of executive refreshes were >50% of original hire grant; 2024 — only 6% reached that threshold. Plan accordingly when modeling retention equity.
