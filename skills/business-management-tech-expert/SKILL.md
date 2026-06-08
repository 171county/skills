---
name: business-management-tech-expert
description: Expert-level knowledge base for managing technology organizations. Use this skill when users ask about tech company org design (Spotify model, squads/tribes, Conway's Law), engineering management (engineering ladders, performance management, 1-on-1 frameworks, DORA metrics), agile at scale (SAFe, LeSS, OKRs), product management (PRDs, RICE/ICE/MoSCoW/WSJF prioritization, roadmaps), technical debt management, build vs. buy decisions, data-driven management (OKRs vs KPIs, North Star metric, leading/lagging indicators), hiring and compensation, culture building (psychological safety, blameless post-mortems, async-first), vendor management (SLA/SLO/SLI), digital transformation, legacy modernization, SOC 2/ISO 27001 compliance, incident response, remote team management, board reporting, or platform engineering. Invoke for any question about running, scaling, or improving a technology organization.
---

# Business Management Tech Expert

Expert-level knowledge for managing technology organizations at scale (2024–2026).

---

## 1. Tech Company Organizational Design

### The Spotify Model: Squads, Tribes, Chapters, Guilds
- **Squad:** Atomic unit. Cross-functional, self-organizing team (6–12 people) with end-to-end ownership of a product area. A product owner owns accountability; the squad operates like a mini-startup. Long-lived teams, not project teams.
- **Tribe:** Collection of squads in related product domains (<150 people — Dunbar's number). Tribe Lead owns the product area.
- **Chapter:** Cross-squad functional community within a tribe. All iOS engineers in a tribe form a chapter. **Chapter Lead is the direct line manager** (performance reviews, salary, growth) while the squad's product owner owns day-to-day work. This resolves the matrix manager conflict.
- **Guild:** Lightweight, voluntary community of practice crossing tribe boundaries. Shares knowledge and develops standards without hierarchy.

**Critical caveat:** Spotify engineers have publicly stated the 2012 whitepaper no longer reflects how Spotify actually operates. It was a snapshot of one experimental approach — not a current blueprint. The principles (product ownership, team autonomy, craft leadership) outlast the labels.

### Conway's Law
Organizations design systems that mirror their own communication structures. Siloed functional departments (all backend in one, all frontend in another) produce siloed architectures. 

**Inverse Conway Maneuver (Team Topologies):** Deliberately design your team structure to produce the desired software architecture. Want loosely coupled microservices? Create loosely coupled, autonomous teams first.

### Functional vs. Product-Led Org
- **Functional org:** Grouped by discipline. Strong craft depth, clear career paths, high cross-functional communication cost.
- **Product-led org:** Cross-functional teams around product areas. Lower delivery communication cost, higher customer alignment. Risk: craft standards erode if chapter leads have no real authority.
- **Reality:** Most mature tech orgs run a hybrid — product-aligned squads for delivery, functional communities for hiring, standards, and career development.

---

## 2. Engineering Management

### Engineering Ladders
**Typical IC Track:**
- L1/L2: Junior — executes well-defined tasks, needs close guidance
- L3: Mid-level — independently completes features, solid code review
- L4: Senior — owns significant systems, leads technical decisions, multiplier for others
- L5: Staff — cross-team impact, drives significant technical initiatives
- L6: Principal — cross-org or company-wide technical strategy
- L7: Distinguished/Fellow — industry-level impact

**Design decisions:**
- **Scope-based** (Dropbox model): Levels defined by scope of impact (individual, team, org, company). Ages better than competency-based.
- **Dual track:** Management track (EM → Director → VP) maps roughly to L4 ↔ EM, L5/L6 ↔ Director.
- **Radford Global Scale:** External compensation benchmarking standard. Map internal levels to Radford levels (P1–P6 IC, M1–M4 management) for competitive benchmarking.

### Performance Management Best Practices
- Continuous feedback model (quarterly check-ins + annual calibrations) replacing annual reviews.
- **Calibration sessions:** Cross-manager panels normalize ratings using the same ladder rubric. Prevent grade inflation.
- PIPs should come with documented prior feedback, specific measurable goals, 30–90 day timeline. A PIP should never be a surprise.

### 1-on-1 Frameworks
**Structure (50 min weekly / bi-weekly for senior ICs):**
- The engineer sets the agenda — they own the meeting, not the manager
- Shared running doc (Notion/Google Doc) updated async between sessions
- Topics that belong: career development, blockers, team dynamics, manager feedback, personal wellbeing, stretch goals
- Topics that don't belong: project status updates (use standups and async tools)

**Signal questions:** "What's something frustrating you that you haven't brought up?" / "If you could change one thing about how the team works, what would it be?" / "What are you learning, and what do you wish you were learning?"

**Career development cadence:** Explicit career conversations quarterly. Use the ladder rubric to co-create a development plan.

### Team Health Metrics (DORA + SPACE)

**DORA Metrics (5 metrics as of 2024):**
1. Deployment Frequency — how often deployments happen
2. Lead Time for Changes — commit to production time
3. Change Failure Rate — % of deployments causing incidents
4. Time to Restore Service — MTTR for production incidents
5. **Rework Rate** (added 2024) — % of deployments that were unplanned fixes for user-facing bugs

Elite performers: multiple deploys/day, lead time <1 hour, failure rate <5%, MTTR <1 hour.

**SPACE Framework (Microsoft Research 2021):** Satisfaction, Performance, Activity, Communication/Collaboration, Efficiency. Prevents single-metric gaming.

**What to avoid:** Lines of code and story points velocity as performance proxies. 65% of engineering leaders explicitly avoid these (Swarmia 2025).

---

## 3. Agile/Scrum at Scale

### SAFe (Scaled Agile Framework)
Used by 70%+ of Fortune 100 companies. Key constructs:
- **Agile Release Train (ART):** 50–125 people organized around a value stream. Plans together, commits together, delivers together.
- **PI Planning:** 2-day quarterly event where all teams align on a 10-sprint (~12-week) roadmap. Produces team PI objectives, program board with dependencies. Most cited as SAFe's highest-value ceremony.
- **Release Train Engineer (RTE):** The ART's chief Scrum Master / servant leader.

**OKR integration:** SAFe 6.0 added OKRs to its "spanning palette." PI Objectives translate strategic OKRs into ART-level commitments.

**Criticisms:** Ceremony-heavy (PI Planning alone = 2 days quarterly for 50–125 people); temporal coupling forces dependencies into synchronized "sprints of sprints," slowing down independent teams; can reinforce waterfall thinking at the PI level.

### LeSS (Large-Scale Scrum)
Philosophy: scale Scrum by removing, not adding, process.
- One product backlog, one product owner for up to 8 teams
- Standard 2-week sprints ending in jointly integrated increment
- No program-level roles (no SAFe equivalents)
- LeSS Huge (8+ teams): adds Area Product Owners

**SAFe vs. LeSS:** LeSS for high team autonomy and genuine product thinking. SAFe for complex dependencies, portfolio management, or regulatory compliance that benefits from structured governance.

### Continuous Delivery at Scale
- Trunk-based development preferred over long-lived feature branches
- Shift-left testing: quality gates at developer workstations and CI pipeline
- **Feature flags:** Decouple deployment from release. Code ships dark, enabled progressively by percentage, user segment, or geography.

---

## 4. Product Management

### PRD Structure (Expert Level)
1. **Problem Statement:** What user/business problem? Include quantified evidence (support tickets, NPS comments, usage data).
2. **Goals and Success Metrics:** Tie directly to OKRs or North Star metric.
3. **Non-Goals:** Explicitly what this does NOT cover. Prevents scope creep.
4. **User Stories / JTBD:** Who is the user, what are they trying to do, why?
5. **Functional Requirements:** What must the system do?
6. **Non-Functional Requirements:** Performance, security, accessibility, compliance.
7. **Designs / Mockups:** Linked Figma.
8. **Open Questions / Risks:** Unresolved decisions, dependencies, risk mitigations.
9. **Launch Plan:** Phased rollout, feature flags, support readiness, comms plan.

Modern PRDs are living documents in Notion or Confluence, linked to Jira/Linear epics.

### Prioritization Frameworks

**RICE (Intercom-originated):** Reach × Impact × Confidence ÷ Effort = RICE Score. Best for quantitative comparison of many features. Weakness: confidence is subjective.

**ICE:** Impact × Confidence × Ease. Simplified RICE. Fast and lightweight for early-stage.

**MoSCoW:** Must Have / Should Have / Could Have / Won't Have. Best for scope negotiation with stakeholders, release planning, MVP definition.

**WSJF (SAFe's Weighted Shortest Job First):** Cost of Delay ÷ Job Duration. Prevents small high-value features from being blocked by large low-value projects. Economically-grounded.

**Kano Model:** Basic (expected), Performance (linear satisfaction), Delighters (unexpected, high satisfaction). Use for strategic differentiation.

**Meta-principle:** RICE for internal feature prioritization, MoSCoW for stakeholder negotiation, Kano for UX strategy, WSJF for SAFe environments. Combine them.

### Roadmap Communication
- **Now / Next / Later:** Avoids commitment to specific dates while communicating direction. Used by Linear, Notion, Intercom.
- **Outcome roadmap:** "Improve checkout conversion by 15%" > "redesign checkout flow." Stakeholders align on outcomes; teams decide solutions.
- Cadence: quarterly roadmap review with leadership, monthly update to stakeholders, weekly sprint demo as evidence.

---

## 5. Technical Debt Management

### Quantification
- CIOs estimate technical debt = 20–40% of their entire technology estate value (before depreciation) — McKinsey.
- 10–20% of new product budget diverted to tech debt resolution.
- Companies with lowest tech debt ratio saw 20% higher revenue growth than highest-debt peers (McKinsey, 220 organizations).

### Debt Classification
- **Intentional / Strategic:** Deliberate shortcuts with known cost, documented with a repayment plan.
- **Unintentional / Reckless:** Poor practices not recognized as debt at the time.
- **Bit rot / Entropy:** Systems that were good but degraded as requirements changed without updates.

### Management Approach
- Dedicate 10–20% of engineering capacity each sprint to debt reduction.
- Maintain a "Tech Debt Register" — backlog of known debt items with estimated remediation cost and business impact.
- Architecture Health Check before major new investment.
- **Modernization options:** Refactor (in-place) → Replatform (new infrastructure) → Rearchitect (structural) → Replace (buy or rebuild).
- **Strangler Fig Pattern (Martin Fowler):** Incrementally replace legacy by routing new functionality to new services while legacy continues running. Reduces big-bang rewrite risk.

### Architecture Decision Records (ADRs)
Lightweight, version-controlled documents capturing architectural decisions, context, and consequences. Use for any decision that would be costly to reverse. RFC process for significant changes: circulate written proposal for async review before implementation.

---

## 6. Build vs. Buy Decisions

**5-Test Framework (CTO-Level):**
1. **Core Competency Test:** Does this represent competitive differentiation? If yes, lean Build.
2. **5-Year TCO:** Include license fees, integration costs, training, vendor support, migration risk. Hidden integration costs add 150–200% to "buy" license fees. Break-even for mid-market: ~33 months.
3. **Time-to-Market:** COTS/SaaS solutions deploy 40–60% faster. Critical when speed matters.
4. **Compliance / Security:** Fintech and healthcare often require custom-built or deeply auditable vendors.
5. **Integration Complexity:** If vendor API requires extensive custom work, the buy advantage erodes quickly.

**Gartner's Composable Architecture:** Buy SaaS for commodity functions (HR, payroll, CRM, observability); build for competitive advantage (core product, proprietary algorithms, unique data pipelines).

**Vendor lock-in mitigation:** Adapter patterns to abstract vendor APIs; OpenTelemetry for vendor-neutral observability; multi-vendor for critical systems; negotiate data portability and exit clauses upfront.

---

## 7. Data-Driven Management

### OKRs vs. KPIs

| Dimension | OKR | KPI |
|---|---|---|
| Purpose | Goal-setting and alignment | Performance monitoring |
| Time horizon | Quarterly | Ongoing |
| Ambition | Aspirational (70% achievement = success) | Target-based (100% = success) |
| Change frequency | Reset each cycle | Relatively stable |

**OKR anatomy:** Objective = qualitative, aspirational ("Make our mobile app the fastest in the category"). Key Results = 2–5 measurable outcomes per objective ("Reduce p95 load time from 3.2s to 1.5s").

**Alignment cascade:** Company OKRs → Department OKRs → Team OKRs. Teams should co-create their OKRs (not receive top-down) to drive ownership.

### North Star Metric
The single metric that best captures the value delivered to customers, predicts long-term retention and revenue, and can be decomposed into team-level inputs.

**Examples:** Airbnb: Nights booked; Slack: Messages sent per active user; Spotify: Time spent listening; LinkedIn: Professionals with 5+ connections.

**NSM hierarchy:** North Star Metric (strategic, long-horizon) → Input metrics/levers (team-level, controllable) → OKRs (quarterly commitments on specific input metrics) → KPIs (health monitoring, prevent gaming of NSM).

### Leading vs. Lagging Indicators
- **Lagging:** Revenue, churn rate, NPS. Easy to measure, hard to act on quickly.
- **Leading:** DAU/MAU ratio, feature adoption rate, support ticket volume trends, time-to-first-value. Actionable and predictive.
- **Goal:** Leading indicators provide 4–8 weeks of early warning before lagging indicators move.

---

## 8. Hiring and Talent

### Technical Interviewing (FAANG-Evolved, 2024–2025)
- **Recruiter screen (30 min):** Eligibility, compensation alignment, timeline.
- **Technical phone screen (45–60 min):** Coding problem (Medium–Hard LeetCode). Google moved to Hard as norm by 2024.
- **System design (45–60 min):** Distributed system design. Senior candidates expected to cover consistency models, failure modes, capacity planning, CAP theorem.
- **Behavioral (45–60 min):** STAR-format answers evaluated against company values.

**Structured interviews** (verified higher validity): All candidates answer the same questions in the same order, evaluated against the same rubric. Reduces bias, improves signal consistency.

**Meta's 2024 change:** Eliminated "bootcamp" onboarding rotation. Moved to upfront team matching — candidate must secure a team match before final offer.

### Compensation (2025 Data)
- FAANG L1 (new grad): ~$170K base. L2: ~$250K. Stock + bonus often adds 50–100% of base at senior levels.
- AI infrastructure, ML systems specialists: packages exceeding $1M total comp at top companies.
- Remote workers at FAANG: location-based pay enforced; full comp requires Tier-1 hub.
- Startup (Series A–C): 30–50% lower base, higher equity %, faster trajectory, more scope.

---

## 9. Culture Building

### Psychological Safety (Amy Edmondson)
Belief that one will not be punished for speaking up with ideas, questions, concerns, or mistakes. Google's Project Aristotle (2016): #1 predictor of team effectiveness, above all other factors.

**Operational practices:**
- Leaders model fallibility: publicly acknowledge their own mistakes
- Separate outcome accountability from process safety
- "Strong opinions, weakly held": vigorous debate without coupling ideas to identity
- Hire for intellectual humility; deselect for arrogance

### Blameless Post-Mortems (From Google SRE Culture)
Core principle: All participants acted in good faith with the information available at the time. The system failed, not the person.

**Standard template:** Incident timeline, contributing factors, impact assessment, detection time, resolution time, action items (owner + due date), future prevention.

**Key practices:**
- Focus on contributing causes, not single root cause (complex systems rarely have one)
- Post-mortem documents are public within the org — normalize failure as learning
- Action items must be tracked to completion — post-mortems without follow-through undermine the culture
- Async post-mortems for distributed teams: each stakeholder adds perspective before sync discussion

### Documentation Culture
**Notion vs. Confluence:** Confluence for compliance-heavy orgs (fintech, healthcare) — stronger permissions, better audit trail. Notion for product/startup environments — better UX, flexible databases.

**Documentation standards:**
- Every service has a README with purpose, runbook link, on-call owner, architecture diagram
- Decisions in ADRs (version-controlled in repo, not wiki)
- Runbooks linked from PagerDuty alerts
- Documentation as code: reviewed in PRs, kept close to the systems they describe

### Async-First Culture
**Async-first principles:**
- Write before you meet: no meeting without an async pre-read document. The meeting is for decisions, not information transfer.
- Default to async for: status updates, decisions with clear options, information sharing. Default to sync for: ambiguous problems, sensitive conversations, real-time collaboration.
- Response time norms: acknowledge within 2 hours (emoji reaction), substantive reply within 24 hours.
- Rotating meeting windows: no group should always hold the "bad" time slot.

---

## 10. Vendor Management

### SLA / SLO / SLI Hierarchy
- **SLI (Service Level Indicator):** Specific metric measuring actual service quality. "% of API requests returning 200 in under 200ms over last 30 days."
- **SLO (Service Level Objective):** Internal target for an SLI. "99.9% of requests return 200 in under 200ms." SLOs drive engineering prioritization — SLO in danger = reliability takes priority over new features.
- **SLA (Service Level Agreement):** Contractual commitment with financial consequences. Set slightly looser than internal SLOs to provide buffer.

**Error budgets:** If SLO = 99.9% availability, error budget = 0.1% = ~43.8 minutes/month. When exhausted, the release train stops until budget is restored.

### Contract Negotiation
Standard vendor SLAs favor the vendor. Areas with room to negotiate even at standard tiers: data portability terms, SLA credit percentages, contract co-termination clauses, data retention policies, audit rights, incident notification timelines.

**Co-termination clause:** All subscriptions in a vendor portfolio renew/terminate at the same date — simplifies negotiation leverage.

**Exit clause:** Explicit data export format, timeline, and cost. Negotiate before signing, not when trying to leave.

---

## 11. Digital Transformation and Legacy Modernization

### Modernization Strategies (Gartner 6 Rs + Extensions)
1. **Retire:** Decommission unused systems. Underestimated ROI.
2. **Retain:** Keep as-is. Valid for stable, non-strategic systems.
3. **Rehost (Lift-and-shift):** Move to cloud without code changes. Fastest, least risk.
4. **Replatform:** Minor cloud optimizations. Some benefit without full rewrite.
5. **Refactor:** Restructure code without changing behavior. Addresses debt without disruption.
6. **Rearchitect:** Change architecture (monolith to microservices). High risk, high reward.
7. **Replace:** Replace with SaaS or new custom build. Cleanest outcome.

**Strangler Fig Pattern:** Incrementally replace legacy by routing new functionality to new services while legacy continues.

### Microservices Migration (When and How)

**When NOT to migrate:**
- Small team (<30–50 engineers): operational overhead exceeds benefits
- Bounded contexts not well-understood: produces "distributed monolith"

**Migration playbook:**
1. Define bounded contexts using DDD — identify natural seams
2. Strangle the monolith: extract at bounded context boundaries, starting highest-velocity areas
3. Establish API gateway before extracting services
4. Separate databases: each service owns its data store (shared DBs are the most common anti-pattern)
5. Invest in observability first: distributed tracing, centralized logging, service mesh health monitoring

---

## 12. Security and Compliance

### SOC 2 Type II
Five Trust Service Criteria (TSC): Security (mandatory), Availability, Processing Integrity, Confidentiality, Privacy. Most B2B SaaS pursue Security + Availability.

**Preparation timeline:** 1–2 months gap assessment → 3–4 months implement controls → 5 months readiness → 6–18 month observation period → audit and report issuance.

**Common controls:** MFA, RBAC, encryption at rest and in transit, background checks, security awareness training, vulnerability management, vendor risk management, incident response procedures, BCP.

### ISO 27001
International ISMS standard. ~80% overlap with SOC 2. ISO 27001 preferred for European customers; SOC 2 for US/North American enterprise. Update to 2022 edition.

### GDPR Compliance
Key requirements: lawful basis documented per processing activity, data subject rights fulfilled within 30 days, DPIA for high-risk processing, DPAs with all sub-processors, 72-hour breach notification to supervisory authority, Records of Processing Activities (RoPA) maintained.

**SOC 2 + ISO 27001 + GDPR:** ~70% of controls overlap. Build a unified control framework and map evidence once to multiple standards.

---

## 13. Crisis Management and Incident Response

### Incident Severity Classification
- **P0/SEV1:** Total outage, data loss, security breach. All hands. Executive notification within 15 minutes.
- **P1/SEV2:** Significant degradation affecting >20% of users. Incident commander + engineers. Customer comms within 30 minutes.
- **P2/SEV3:** Partial degradation, workaround available. Normal engineering response.
- **P3/SEV4:** Minor issue. Normal sprint process.

### Incident Commander Role
One person owns coordination — not the most senior, not the most knowledgeable, but the person trained to run the process. Separates diagnosis (engineers) from coordination (IC).

### Communication Templates
- Status page update every 30 minutes minimum during P0/P1: "We are investigating reports of [X]. Our engineering team is actively working. Next update in 30 minutes."
- Executive summary: impact scope + known cause + mitigation in progress + ETA + who is on it.
- Customer-facing: acknowledge quickly, communicate impact without jargon, commit to update cadence.

Only ~55% of companies globally maintain fully documented IR plans (2025). Teams with documented procedures see 30–50% faster resolution times.

---

## 14. Remote Team Management

### Async Communication Stack
- **Slack/Teams:** Real-time and async messaging. Channel naming conventions: `#team-[name]`, `#proj-[project]`, `#inc-[incident]`.
- **Linear:** Issue tracking with opinionated workflow, fast UI, strong GitHub integration.
- **Notion:** Knowledge management, product specs, wikis, database-driven project tracking.
- **Loom:** Async video for complex explanations — more context than text, more timezone-respectful than synchronous.
- **Miro / FigJam:** Async visual collaboration, architecture whiteboarding, async retros.

**Documentation-first culture:** A new team member joining should understand any decision by reading the artifacts without asking anyone.

---

## 15. Executive Communication

### Board Reporting
**What boards want:** Honest over polished; strategy + execution link; risk and mitigation; exception-based reporting (anomalies, emerging risks, decisions requiring board input).

**Board pack structure:**
1. Executive summary (1 page): headline metrics vs. targets, top 3 developments, key decisions needed
2. Financials: revenue, ARR, burn rate, runway, unit economics vs. forecast
3. KPI dashboard: North Star, DORA metrics, NPS — trended, not point-in-time
4. Product and technology update: roadmap status, major releases, significant incidents
5. Risks: updated risk register with changes highlighted
6. Outlook: next quarter priorities and commitments

### Investor Updates (Monthly/Quarterly)
**Standard format:** Highlights (top 3 wins) + Lowlights (top 3 challenges — required; builds trust) + Key metrics (MRR/ARR, growth, CAC, LTV, NRR, headcount) + Asks (specific introductions, hires, partnerships) + Next period focus.

**Expert principle:** Investors remember founders who were transparent during hard times. Over-communication during difficulty builds more trust than silence followed by emergency fundraising.

---

## 16. Platform Engineering

**Platform team treats developers as their customers.** IDP (Internal Developer Platform) components:
- Self-service infrastructure provisioning (Terraform-based, via UI or CLI without opening a ticket)
- Golden path templates (starter repos, CI/CD configs, observability auto-instrumentation)
- Developer portal (Backstage is the open-source standard — service catalog, tech radar, documentation)
- Security as a default: hardened base images, secret management (Vault), dependency scanning
- FinOps integration: cost visibility per service/team

**Impact:** Teams using IDPs report 21% improvement in developer productivity, driven by reduced infrastructure setup toil.

**Gartner:** Platform engineering is a Top 10 strategic trend for 2024–2025; 80% of large orgs projected to have platform teams by 2026.

### When to Standardize vs. Allow Polyglot
**Golden path model:** Define official supported, documented, tooled, monitored paths for standard workloads. Engineers following the golden path get free CI/CD, security scanning, observability, and platform support. Allow "off-path" choices with explicit trade-offs: team takes operational ownership, no platform support, architectural review required.
