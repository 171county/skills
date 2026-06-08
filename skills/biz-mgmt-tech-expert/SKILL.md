---
name: biz-mgmt-tech-expert
description: Expert-level business management for tech companies and engineering organizations. Use this skill whenever a user asks about engineering team structure, org design, agile methodologies, DORA metrics, technical debt, engineering hiring, PM-engineering partnerships, engineering leadership (EM/TL/Director/VP/CTO), AI-augmented development teams, platform engineering, DevOps, vendor management, build vs buy decisions, or reporting engineering performance to executives and boards. Trigger on any question involving how to run, structure, measure, grow, or lead a software engineering team or tech company — even if the user doesn't use technical management jargon.
---

# Business Management Tech Expert

You are an expert practitioner in managing technology companies and engineering organizations. You draw on current (2024–2026) frameworks, empirical research, and practitioner knowledge to give specific, actionable, expert-grade guidance. Avoid generic advice — cite frameworks, name tradeoffs, give concrete numbers when available, and flag where conventional wisdom diverges from evidence.

Reference the sections below for the relevant domain. For multi-domain questions, synthesize across sections.

---

## 1. Engineering Organization Design

### Team Topologies (Skelton & Pais, 2nd ed. 2025)
The canonical modern framework. Four fundamental team types:

- **Stream-aligned teams**: Aligned to a flow of customer-facing work (product, feature domain, user journey). Own delivery end-to-end. This should be the most common type (~65–80% of teams). Key metric: fast flow without hand-offs.
- **Platform teams**: Build and operate internal services/tooling that stream-aligned teams consume as self-service. Treat the platform as an internal product — measure adoption voluntarily, not mandated. Must reduce cognitive load on stream-aligned teams; if they don't use it by choice, it's failing.
- **Enabling teams**: Small, temporary coaches. Embed with stream-aligned teams to uplift a capability (e.g., security practices, observability), then move on. Never become a permanent dependency.
- **Complicated-subsystem teams**: Own components requiring deep specialist knowledge (ML pipeline, real-time pricing engine, cryptographic library). Only create when cognitive load of the subsystem would overwhelm a stream-aligned team.

**Three interaction modes**:
1. Collaboration (working together for discovery — time-limited)
2. X-as-a-Service (one team consumes another's capability with minimal interaction)
3. Facilitating (enabling team temporarily coaches another)

**Cognitive load is the key constraint.** Teams struggling to understand their own codebase are too large or have too wide a scope. Typical upper bound: 5–9 people (Dunbar sub-group). Split by domain, not by layer (frontend/backend splits create coordination costs).

**Org design after AI (2025 insight from Thoughtworks)**: AI agents are beginning to function as pseudo-team members. Org design is evolving toward human-AI hybrid teams where AI handles rote work, shifting team topology thinking toward cognitive bandwidth allocation rather than headcount.

### Spotify Squad Model — Critiques (2024–2025)
The Spotify model (squads, tribes, chapters, guilds) was never meant to be a prescriptive framework — it was a 2012 snapshot of how Spotify organized at that moment. Key failures in adoption:

- **Scaling coordination**: Squad autonomy breaks down when dependencies between squads grow. The model has no strong mechanism for cross-squad dependency management.
- **Chapter manager problem**: Chapter leads have people management authority but not delivery authority, creating split accountability that leads to confusion.
- **Spotify itself abandoned it**: By 2020, Spotify had moved away from this exact structure.
- **What to use instead**: Team Topologies provides a more rigorous, interaction-mode-aware framework. Netflix and other mature orgs use variants with explicit platform/enabling team separation.

### Engineering Manager vs. Tech Lead
| Dimension | Tech Lead | Engineering Manager |
|-----------|-----------|---------------------|
| Primary accountability | The system | The people |
| Code contribution | Active (writes and reviews code) | Minimal to none |
| Career development | Technical mentorship | Full career ownership |
| Hiring/firing authority | None | Yes |
| Process ownership | Influences | Owns |
| Performance reviews | Input | Authors |

Both roles can exist on one team; in small teams (<6 engineers) one person often fills both. Separate roles as the team grows past ~8. The TL-EM pairing is a high-leverage structure at scale.

### Span of Control
- **Engineering Manager**: 5–8 direct reports is the practitioner sweet spot for active coaching. Netflix runs ~5–6 per EM; Meta allows up to ~12 for experienced managers.
- **Gallup (2025 data)**: Average span has grown from 10.9 (2024) to 12.1 (2025) as AI absorbs coordination overhead, but median remains ~5–6. Spans above 10 signal under-investment in management.
- **Directors**: 3–5 EMs (15–40 engineers total). At this level, managers need a manager.
- **VP Engineering**: Owns organizational delivery. Typically 3–6 Directors.

### SRE Teams
Site Reliability Engineering teams operate in two primary topologies:
1. **Central SRE team** (Google model): Embedded SRE rotates into product teams for a time-box to improve reliability, then returns. Maintains error budget framework.
2. **Embedded SRE**: SRE lives permanently in a stream-aligned team. Higher context, lower consistency.

Google SRE principle: SREs refuse to take on pager duty for a service that doesn't meet a defined reliability bar. Error budgets (1 - SLO) govern release velocity — burn the budget, freeze releases until it recovers.

---

## 2. Agile & Modern Engineering Management

### Scrum vs. Kanban vs. Shape Up

**Scrum**: Fixed-length sprints (1–2 weeks), defined ceremonies (planning, daily standup, review, retro), explicit roles (PO, SM, Dev). Best for teams with predictable work and stable requirements. Main failure mode: treating sprints as mini-waterfalls; backlog refinement becomes a bureaucratic burden.

**Kanban**: Pull-based flow with WIP limits. No time-boxes. Best for support/ops work or teams where work is highly variable in size. Key metrics: cycle time, throughput, WIP. Avoid Kanban without WIP limits — it's just a to-do list.

**Shape Up (Basecamp)**: 6-week cycles with 2-week cool-down. Core concepts:
- **Appetite**: "How much is this worth?" replaces estimation. Teams fix time, scope is variable.
- **Shaping**: Senior people do upfront problem-solving and de-risk work before betting on it. Shaped pitches go to the **betting table** (not a backlog — unshouldered work simply doesn't happen).
- **Hill charts**: Visual progress tracking distinguishing "figuring out" (uphill) from "making it happen" (downhill).
- Best for: Product companies with stable teams, where PMs and senior engineers can do shaping work. Poor fit for services teams or teams with high interrupt work.

**2025 trend**: Many teams are hybridizing — using Kanban for operational work and Shape Up for product development tracks.

### DORA Metrics (2024 State of DevOps Report — Google)
The four core metrics with 2024 benchmarks:

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Deployment Frequency | On demand (multiple/day) | Daily–weekly | Weekly–monthly | Monthly or slower |
| Lead Time for Changes | < 1 day | 1 day–1 week | 1 week–1 month | > 1 month |
| Change Failure Rate | ~5% | ~10% | ~15% | ~20%+ |
| Failed Deployment Recovery (MTTR) | < 1 hour | < 1 day | 1 day–1 week | > 1 week |

**2024 distribution**: Elite = 19% of respondents, High = 22% (down from 31%), Medium = stable, Low = 25% (up from 17%). The polarization of performance is a notable 2024 finding.

**Key relationships**: Elite performers deploy 182× more frequently than low performers, have 127× faster lead times, and 8× lower change failure rates. Stability and speed are positively correlated — not tradeoffs.

**2024 additions**: DORA added a 5th metric (reliability) and began measuring AI impact. A 2025 refinement adds rework rate as a key signal.

**AI finding from 2024 DORA**: AI tool users are more likely to report experimental failures, suggesting AI accelerates discovery of what doesn't work — a net positive for learning-oriented cultures.

### Velocity Measurement Pitfalls
Story points/velocity is widely misused:
- **Goodhart's Law applies**: When velocity becomes a target, teams inflate estimates. Velocity comparisons across teams are meaningless.
- **Use velocity for capacity planning within a team, never for comparison or performance review.**
- **Better signal**: DORA metrics, cycle time distribution (median + p85), and flow efficiency (time active vs. time waiting).

### SPACE Framework (Forsgren et al., 2021)
Five dimensions of developer productivity: Satisfaction, Performance, Activity, Communication/Collaboration, Efficiency/Flow. Key insight: no single metric captures developer productivity. Activity metrics (lines of code, PRs merged) are the weakest proxy; flow state interruptions are among the most damaging factors.

---

## 3. Technical Debt Management

### Quantification Approaches
- **Stripe Developer Coefficient Report**: Developers spend ~42% of working time on technical debt and rework (~13.5 hrs/week on tech debt, ~3.8 hrs on bad code). Estimated $85B/year in global opportunity cost.
- **McKinsey**: Organizations that actively manage tech debt free up engineers to spend up to 50% more time on business-value work. ~30% of CIOs believe >20% of tech budget is diverted to tech debt.
- **SonarQube SQALE method**: Assigns remediation time estimates to code issues; aggregates into a debt ratio (remediation time / development time). Crude but automatable.
- **Business impact framing**: Convert to $ using (dev time lost × fully-loaded hourly rate) + (incident cost × frequency). This is what executives respond to.

### Communicating to Executives
Use a financial metaphor — tech debt is borrowed time that accrues interest. Frame it as:
- **Principal**: The deferred work itself
- **Interest**: The ongoing drag (slower feature delivery, higher defect rate, harder onboarding)
- **Risk multiplier**: Probability × impact of a debt-triggered failure

Present a **tech debt portfolio**: categorize debt into (a) strategic (intentional trade-off, plan to repay), (b) tactical (shortcuts under time pressure), and (c) reckless (no awareness it was debt). Executives can engage with strategic debt; reckless debt is a governance failure.

Target: ~20% of every sprint allocated to debt reduction (the "Boy Scout Rule" applied systematically). Some teams run dedicated "debt sprints" — this tends to be less effective than continuous allocation.

### Strangler Fig Pattern
The dominant approach for legacy modernization (Martin Fowler, named after the strangler fig tree). Mechanics:
1. Build new behavior alongside old system, not inside it
2. Route new traffic/features to the new system via a facade layer
3. Progressively strangle the old system until it can be deleted

Advantages over "big bang" rewrites: delivers value continuously, can halt mid-way, limits risk surface at any given time.

### Architecture Decision Records (ADRs)
Lightweight documents capturing: Context → Decision → Consequences (positive and negative). Tools: adr-tools CLI, log in `/docs/adr/`. Key practice: treat ADRs as active governance artifacts, not passive documentation. Reference ADRs in PR reviews when relevant. A "Debt-Aware ADR" variant explicitly models drawbacks as interest payments.

### Rewrite vs. Refactor Decision Framework
- **Refactor when**: The domain model is sound, the codebase is comprehensible, debt is localized, and team has context.
- **Rewrite when**: The system's architecture is fundamentally incompatible with current requirements, the team cannot safely navigate the existing code, and the risk of incremental change exceeds the risk of replacement.
- **The Rewrite Warning**: Joel Spolsky's rule — "never rewrite from scratch" — remains largely valid. Most rewrites underestimate scope and lose embedded domain knowledge. The strangler fig is almost always preferable.
- **Signals that a rewrite may be justified**: >50% of dev time going to keep-the-lights-on, inability to onboard new engineers in < 2 weeks, security vulnerabilities baked into the architecture.

---

## 4. Hiring & People Management

### Technical Interview Design
Current practitioner consensus (2024–2026):

| Format | Pros | Cons | Best for |
|--------|------|------|---------|
| LeetCode/algorithmic | Objective, scalable | Tests interview prep, not job performance; alienates senior engineers | Screening volume; junior roles at FAANG |
| System design | Tests real-world thinking, scales well | Subjective; requires skilled interviewers | Mid-senior engineers |
| Take-home project | Closest to real work | Expensive for candidates; bias toward time-rich candidates | Senior/staff roles at product companies |
| Paid work simulation | Eliminates bias in take-home | Expensive operationally | High-stakes senior hires |

**2026 shift**: The "Leetcoding era" (2020–2024) is transitioning to systemic reasoning. Companies like Google, Stripe, Airbnb are reducing pure algorithmic puzzles in favor of real-world scenario discussions and architecture reviews.

**Practical recommendation**: 1 take-home or code review of existing code + 1 system design + 1 behavioral (structured STAR). Avoid brain teasers entirely — no evidence of validity.

### Compensation Benchmarking
- **Levels.fyi**: Crowdsourced; best for US tech market, FAANG/growth-stage accuracy. Use for competitive intelligence, not band-setting.
- **Radford (Aon)**: Institutional survey; most credible for setting formal salary bands. Requires company participation. Covers base, bonus, equity separately.
- **Approach**: Set bands at P50 of market for competitive roles; P65–P75 for scarce skills (ML, security). Refresh at least annually — the 2022–2024 correction means 2021 benchmarks are stale.
- **Equity**: RSU refresh schedules matter as much as grants. Document the refresh cadence explicitly in offer letters.

### Performance Reviews
- **Continuous feedback > annual reviews**: Annual reviews create "feedback debt" and recency bias. Best practice: weekly 1:1s with ongoing feedback, quarterly career conversations, annual calibration.
- **Calibration committees**: Cross-manager calibration prevents rating inflation and ensures consistency. Run them before communicating ratings.
- **Rating distributions**: Forced ranking (stack ranking) is widely discredited. Creates internal competition, destroys psychological safety. Use distribution guidance (e.g., "expect ~15% exceeds, ~70% meets, ~15% below") without forced ranking.

### Performance Improvement Plans (PIPs)
- **Rise of PIPs**: HR Acuity data shows 43.6 per 1,000 US workers on formal performance procedures in 2023, up ~30% from 2020.
- **Effectiveness**: PIPs have poor success rates when used as a prelude to termination rather than genuine development. Engineers on PIPs typically exit within 90 days regardless of outcome.
- **Best practice**: Use PIPs only when (a) the performance issue is clearly defined, (b) the employee was unaware of the gap, and (c) there is genuine organizational commitment to supporting improvement. Document everything.
- **Skill vs. will diagnosis**: Before a PIP, determine if the gap is capability (skill) or motivation (will). These require fundamentally different interventions.

### 1:1 Frameworks
Effective 1:1s are not status reports. Canonical structure:
1. **Check-in** (2 min): How is the person doing personally/professionally?
2. **Their agenda** (20 min): What's on their mind? Career, team dynamics, blockers?
3. **Your agenda** (10 min): Feedback, context, asks
4. **Wrap** (3 min): Any commitments?

Run 1:1s at minimum every 2 weeks for 30–45 minutes. Keep a shared running doc — both parties add agenda items async. Never cancel 1:1s; reschedule within 48 hours if necessary.

### Managing Distributed/Remote Teams
- **Async-first principle**: Default to written communication. Over-document decisions in a shared knowledge base (Notion, Confluence).
- **Time zone management**: Teams spanning >6 hours require explicit overlap windows. Do not schedule critical meetings outside these windows.
- **Proximity bias is real**: Remote engineers get fewer promotions and informal recognition. Counter with explicit calibration prompts in review cycles.
- **In-person investment**: Quarterly or semi-annual team gatherings disproportionately build trust. Even 3 days/year of in-person time shows measurable team cohesion improvement.

---

## 5. Product Management Integration

### PM-Engineering Partnership Models
- **PM as CEO of the product** (Amazon model): PM owns the "what" and "why"; engineering owns the "how." PM writes the PR/FAQ (press release + FAQ) before a spec is written. Clear but can reduce engineering ownership of product decisions.
- **Embedded PM** (Spotify, Google): 1 PM per stream-aligned team, high collaboration. Works when PM quality is high; breaks down when PMs become ticket-writers.
- **Product trio** (Teresa Torres): PM + designer + engineer collaborate on discovery together. Reduces "hand-off" failure mode. Gaining adoption in 2024–2025.

### Product Discovery Frameworks
- **Dual-track agile**: Separate discovery track (experiments, user interviews, prototypes) running in parallel to delivery track. Discovery informs the next delivery cycle.
- **Continuous discovery habits** (Torres): Weekly touchpoints with customers, opportunity solution trees to map problems before solutions.
- **Definition of Done (DoD)**: Explicit checklist agreed by team. Typically includes: tests written and passing, documentation updated, feature flags configured, analytics instrumented, security review complete, and accessibility verified.

### OKR Cascading for Product Teams
- **Structure**: Company → Department → Team OKRs. Max 3 objectives per level, 3–5 key results per objective.
- **Engineering vs. product OKRs**: Engineering OKRs should address platform reliability, developer productivity, and technical risk reduction — not just feature delivery. Mix: ~70% outcome-based (user/business metrics), ~20% health metrics, ~10% innovation.
- **Anti-pattern**: Output-based KRs ("ship X features") measure activity, not outcomes. Rewrite as "increase [metric] by [N]% by [date]."
- **Roadmap negotiation**: Use "Now / Next / Later" horizon framing. Commitments are "bets," not contracts. Explicitly negotiate what gets dropped when new priorities emerge — never just absorb new work.

---

## 6. Engineering Leadership

### Role Distinctions
| Role | Primary Focus | Horizon | Reports to |
|------|---------------|---------|-----------|
| Tech Lead | Technical quality of one team | Weeks–months | EM |
| Engineering Manager | People + delivery for one team | Quarters | Director/VP |
| Senior EM | EMs + people leadership | Quarters | Director |
| Director of Engineering | Cross-team execution, org health | Quarters–year | VP |
| VP of Engineering | Organizational delivery, process, culture | 1–2 years | CTO/CEO |
| CTO | Technology strategy, external positioning, architecture vision | 3–5 years | CEO/Board |

**CTO vs. VP Engineering**: The canonical model — CTO owns the "what" and "why" of technology strategy; VP Engineering owns the "how" and "when" of delivery. CTO faces outward (investors, tech community, recruiting brand); VP faces inward (teams, process, execution). Spencer Stuart (2021–2024): 70% of digital-first companies added CTO to board during this period.

### Technical Vision Documents
A technical vision document should answer:
1. What is the current state? (Honest assessment, not flattering)
2. What is the desired future state in 3 years?
3. What principles guide decisions in ambiguous situations?
4. What are the key architectural bets we are making?
5. What are we explicitly not doing?

Keep under 10 pages. Socialize via engineering all-hands, embed in onboarding. Revisit annually. Good examples of principle-based tech culture docs: Netflix "Freedom and Responsibility" culture memo, Amazon's 16 Leadership Principles (customer obsession, bias for action, ownership, etc.), Google SRE Book (error budgets, toil reduction, blameless postmortems).

### Managing Up (Board/Investors)
- Translate metrics into business language: "Lead time improved 40%" → "We can now respond to a market opportunity in 3 days instead of 3 weeks."
- **Technology risk communication**: Use a risk register framing. For each risk: likelihood, impact, current mitigation, residual exposure. Boards understand portfolio risk language.
- **Engineering budget justification**: Frame as investment tranches — (a) run-the-business (keep lights on), (b) grow-the-business (product velocity), (c) transform-the-business (architecture modernization). Boards should understand what each tranche buys.
- **42% of CTOs** cite "inability to quantify technical value" as their biggest challenge securing funding (2024 survey data). Gartner: 73% of IT projects fail to deliver expected returns partly due to inadequate ROI framing at the outset.

### Engineering All-Hands Best Practices
- Cadence: Monthly or quarterly. Monthly for fast-growth or change-intensive periods.
- Structure: Company context (10 min) → Engineering metrics vs. goals (10 min) → Team spotlights (15 min) → Q&A/anonymous questions (20 min).
- Always address the question engineers actually have: "Is the company doing well? Is my work meaningful? Am I growing?"

---

## 7. AI-Augmented Development Teams (2025–2026)

### Tool Landscape
| Tool | Market Position | Best For |
|------|----------------|---------|
| GitHub Copilot | Enterprise leader; 26M+ users, 50K+ orgs | Large orgs with existing GitHub; integration-first |
| Cursor | Developer favorite; 18% workplace adoption | Autonomy-first developers; complex refactoring |
| Claude Code | Strongest reasoning; 18% workplace adoption | Complex tasks, multi-file reasoning, agentic workflows |

### Productivity Evidence (Verified Research)
- **Controlled task studies (GitHub/Microsoft)**: 55% faster task completion on isolated, well-defined tasks (1h11m vs. 2h41m). 12–22% more PRs merged per week at Microsoft and Accenture in field experiments.
- **The METR RCT (Oct 2024, arXiv:2410.18334)**: 16 experienced open-source developers. AI assistance led to **19% increase in completion time** (not decrease). Developers predicted a 24% speedup before tasks, and estimated 20% speedup after — a ~40 percentage point miscalibration. Interpretation: on high-context, real-world repos, AI imposes net costs that skilled developers fail to anticipate.
- **Resolution**: Evidence is task-complexity dependent. AI tools accelerate well-scoped, lower-context tasks (boilerplate, tests, standard patterns). They can slow experienced engineers on complex, unfamiliar codebases due to context-switching and verification overhead.

### Security and Quality Concerns
- 29.1% of Python and 24.2% of JavaScript generated by Copilot contain security weaknesses across 43 CWE categories (empirical study, ResearchGate 2024).
- Repos using Copilot show 6.4% secret leakage rates — 40% higher than typical repos.
- PR issues in AI-generated code run ~1.7× higher than human-written code.
- **Mitigation**: SAST/DAST pipelines, mandatory code review with security checklist, GitHub Advanced Security or equivalent, and developer training on AI-specific failure modes.

### Less Experienced Developer Dependency Risk
Empirical evaluation found developers with <3 years experience showed **28% lower algorithmic problem-solving performance** after 6 months of continuous Copilot use when tested without AI support. This is a skills atrophy concern. Counter: require regular "AI-off" exercises and system design practice.

### Measuring ROI
Measure three things:
1. **Deployment frequency and lead time** (DORA) — if AI is helping, these should improve
2. **Rework rate** — AI-generated code that must be heavily revised is negative ROI
3. **Developer satisfaction** (SPACE framework) — sustained negative sentiment predicts attrition
Avoid: measuring lines of code generated (vanity metric), measuring Copilot acceptance rate alone (accepted ≠ good).

### Headcount Implications
The honest answer in 2025: net headcount reduction from AI coding tools is not yet empirically documented at scale in engineering (as distinct from other white-collar roles). The more common pattern is productivity absorption into increased scope, not reduced headcount. Budget for 15–25% increased feature throughput rather than headcount reduction. As agentic systems (Claude Code agents, Devin-class tools) mature through 2025–2026, this calculus is shifting — model quarterly.

---

## 8. DevOps & Platform Engineering

### Internal Developer Platforms (IDPs)
An IDP is the sum of tools, services, and workflows a platform team provides for stream-aligned teams to self-serve their infrastructure and deployment needs. Key components:
- Service catalog
- Infrastructure provisioning (Terraform/Pulumi templates)
- CI/CD golden paths
- Secrets management
- Observability defaults (logging, metrics, tracing pre-wired)
- Incident runbooks

**Adoption (2025)**: Gartner predicts 80% of large engineering orgs will have an IDP by 2026. Current data: 90% of orgs report using an IDP, 76% have dedicated platform teams. 55% adopted platform engineering in 2025 alone.

### Golden Paths
Originated at Spotify to solve "rumour-driven development." A golden path is a pre-built, opinionated workflow (often a template + CI/CD pipeline + docs) that encodes best practices for a given type of service (e.g., "create a Python microservice with observability"). Key principles:
- Voluntary adoption — if developers route around it, the path is wrong
- Maintained by the platform team with SLAs
- Measure adoption rate as a primary IDP health metric
- 2024 research: developer independence (ability to self-serve without asking the platform team) yielded a 5% productivity improvement

### DevEx Measurement
DevEx (Developer Experience) measures how it feels to develop. The three core dimensions (Noda, Forsgren et al., 2023):
1. **Flow state** — how often are developers in deep focus?
2. **Feedback loops** — how fast and clear is feedback from CI, code review, production?
3. **Cognitive load** — how much complexity must a developer hold in their head?

Complement DORA (tells you *if* you're shipping) with DevEx surveys (tells you *why*). Run quarterly DevEx pulse surveys. Tools: DX (the company), Pluralsight Flow, LinearB.

### CI/CD Pipeline Principles
- Fail fast: tests should fail within 10 minutes on the main happy path
- Trunk-based development > long-lived feature branches (DORA research shows clear correlation with performance)
- Feature flags decouple deployment from release — enable dark launches, canary rollouts, kill switches
- Pipeline ownership: "you build it, you own the pipeline" — platform team provides the golden path, stream-aligned team owns pipeline configuration

### Incident Management
**Severity levels** (canonical): P0 = complete outage/data loss; P1 = major degradation affecting majority of users; P2 = partial degradation; P3 = minor issue.

**On-call rotations**: Google SRE recommendation — max 2–3 actionable incidents per shift. Above that, you have an alerting problem, not a staffing problem. On-call should never exceed 50% of an engineer's time (including preparation). Burnout from chronic on-call is a leading cause of senior engineer attrition.

**Blameless postmortems** (Google SRE, PagerDuty): Focus on system failures, not individual failures. Assume everyone acted with good intent and best available information. Standard template: summary → impact → timeline → root cause(s) → action items (mitigative + preventative, with owners and due dates). Publish postmortems internally — culture of transparency accelerates systemic improvement.

**Runbooks**: Every P1/P2 scenario should have a runbook. Engineers responding at 3am without runbooks make decisions under stress with incomplete context, extending MTTR. Runbooks should be tested quarterly (fire drills).

---

## 9. Vendor & Tool Management

### Build vs. Buy Framework
Decision matrix — score each dimension 1–5:

| Criterion | Build | Buy |
|-----------|-------|-----|
| Strategic differentiation | High differentiation → Build | Commodity capability → Buy |
| TCO (3–5 year) | Estimate fully-loaded (dev + ops + opportunity cost) | Estimate fully-loaded (license + implementation + integration) |
| Time to market | Custom takes longer | Commercial faster to start, slower to adapt |
| Integration fit | Perfect fit possible | May require compromise or middleware |
| Team expertise | Have the skills | Lack the skills |
| Scalability requirements | Highly custom needs → Build | Standard SLAs sufficient → Buy |

**Modern reframe**: The question is no longer binary. "Build the differentiator, buy the commodity, integrate everything." Forrester: 67% of software projects fail partly due to wrong build/buy choices.

**Outsource as third option**: When capability gap is temporary or specialized. Requires strong IP, data handling, and transition-back clauses in contracts.

### Tech Stack Governance
- **Technology radar** (ThoughtWorks model): Categorize all tools/frameworks as Adopt, Trial, Assess, or Hold. Publish quarterly.
- **RFC process for major changes**: Any change to the core tech stack requires a written Request for Comments with a 2-week comment period before adoption.
- **Shadow IT management**: Gartner estimates 30–40% of enterprise IT spending is shadow IT. Root cause is usually slow official procurement. Counter with: approved self-service catalog, fast-track procurement lane for common SaaS tools (<$10K/yr), and quarterly shadow IT audits via CASB tooling.

### SOC 2 Vendor Review Process
For any vendor processing customer data or accessing your production systems:
1. Request SOC 2 Type II report (not just Type I)
2. Review the report for exceptions — any noted exceptions require mitigation evidence
3. Complete security questionnaire (VSA or CAIQ format)
4. Data Processing Agreement (DPA) with specific retention/deletion clauses
5. Annual review trigger — set calendar reminders

**Vendor tiering**: Tier 1 (critical/strategic, full due diligence annually) → Tier 2 (important, biennial) → Tier 3 (commodity, self-attestation). Most teams find 60–70% of vendors are Tier 3.

### Enterprise Software Negotiation
- Always negotiate; list price is rarely final price. Benchmark using G2, TrustRadius buyer communities, and informal peer networks.
- Key levers: multi-year commit (20–30% discount typical), seat count flexibility, payment terms, API rate limit increases, SLA credits.
- Renewal timing: start 90 days before renewal. Vendors will discount most aggressively at 30 days out.
- Avoid vendor lock-in for core data: always negotiate data export rights in standard formats at contract signing.

---

## 10. Executive Reporting & Board Relations

### Engineering Metrics for Boards
Board-level engineering metrics should be sparse, business-connected, and stable (same metrics each quarter for trend visibility). Recommended dashboard:

| Category | Metric | Why boards care |
|----------|--------|----------------|
| Delivery velocity | Lead time for changes (DORA) | Speed to respond to market |
| Reliability | P0/P1 incident count + MTTR | Business risk |
| Quality | Change failure rate | Customer experience |
| Capacity | % engineer time on product vs. maintenance | Efficiency signal |
| Talent | Attrition rate, open req age | Execution risk |
| Debt | Tech debt ratio or investment % | Balance sheet analog |

### Technology Risk Communication
Use a risk register format boards recognize. For each material risk:
- **Description** (plain English)
- **Likelihood** (H/M/L)
- **Business impact** (revenue at risk, regulatory exposure, reputation)
- **Current mitigation**
- **Residual risk** after mitigation
- **Owner** and **review date**

Common material tech risks: concentration on a single cloud provider (resilience), critical system on unsupported software (security), key-person dependency for critical systems (continuity), regulatory non-compliance in data handling (legal).

### Roadmap Presentation to Non-Technical Executives
- Lead with business outcomes, not technical features. "This quarter we're enabling real-time pricing, which supports the margin expansion objective" not "we're migrating the pricing service to Kafka."
- Use a **three-horizon roadmap**: Horizon 1 (committed, this quarter), Horizon 2 (likely, next 2 quarters), Horizon 3 (exploratory, 12+ months). Be explicit about confidence levels.
- Always show what you're **not** doing and why — demonstrates judgment, not just capacity.
- Quantify the cost of doing nothing (opportunity cost of tech debt, risk of not investing in infrastructure).

### Engineering Budget Justification
Frame budget in three investment tranches:
1. **Run** (~40–60%): Keeping existing systems operational, security patching, compliance
2. **Grow** (~30–40%): Feature development directly tied to revenue or retention OKRs
3. **Transform** (~10–20%): Architectural modernization, platform investment, reducing future run costs

Show year-over-year trend. If "Run" is growing as a % of budget, you have a tech debt problem. If "Transform" is zero, you're accumulating debt. Boards understand capital allocation frameworks — this maps directly to their mental model.

---

## Quick Reference: Framework Decision Tree

**Organizing teams?** → Team Topologies (stream-aligned first, platform only when proven need)

**Choosing a delivery methodology?** → Scrum for stable-scope work; Kanban for flow/ops; Shape Up for product companies with strong shaping capability

**Measuring engineering health?** → DORA (delivery performance) + SPACE/DevEx (developer experience) + business outcome metrics

**Managing technical debt?** → Quantify as $ impact, maintain ADRs, allocate 20% continuous capacity, use strangler fig for legacy migration

**Interviewing engineers?** → Code review or take-home + system design + structured behavioral. No brain teasers.

**Evaluating AI coding tools?** → Measure DORA + rework rate + developer satisfaction. Invest in security scanning. Expect gains on scoped tasks, scrutinize complex codebase work.

**Vendor decision?** → Build for differentiators, buy for commodity, use build/buy TCO matrix for edge cases

**Reporting to board?** → 6 metrics max, business language, risk register format for concerns, three-horizon roadmap

---

*Sources supporting this skill (2024–2026 verified):*
- [2024 DORA State of DevOps Report](https://dora.dev/research/2024/dora-report/)
- [Team Topologies 2nd Edition (IT Revolution, Sep 2025)](https://itrevolution.com/product/team-topologies/)
- [Dear Diary: RCT of Generative AI Coding Tools (arXiv:2410.18334)](https://arxiv.org/abs/2410.18334)
- [GitHub Copilot Impact on Developer Productivity (arXiv:2302.06590)](https://arxiv.org/abs/2302.06590)
- [Stripe Developer Coefficient Report](https://stripe.com/files/reports/the-developer-coefficient.pdf)
- [Google SRE Book & Workbook](https://sre.google/sre-book/table-of-contents/)
- [Shape Up — Basecamp](https://basecamp.com/shapeup)
- [Spotify Model Rise & Fall (DeCo CMS, 2025)](https://www.decocms.com/blog/post/spotify-model-rise-fall-ai-changes-2025)
- [Platform Engineering Trends (CNCF, Nov 2025)](https://www.cncf.io/blog/2025/11/19/what-is-platform-engineering/)
