---
name: business-management-tech-expert
description: Activate this skill when the user asks about managing technology teams, engineering organizations, product management, engineering metrics, team structure, developer productivity, technical leadership, or organizational design for tech companies. Covers Spotify model, Team Topologies (stream-aligned, platform, enabling, complicated-subsystem teams), DORA metrics (deployment frequency, lead time, change failure rate, MTTR) with elite benchmarks, SPACE and DevEx frameworks for developer productivity, engineering management best practices (1:1s, career ladders, performance reviews, hiring pipelines, retention), product management (PRD structure, RICE/MoSCoW/Kano prioritization, roadmapping, OKRs, North Star Metric), agile methodologies (Scrum, Kanban, Shape Up by Basecamp), DevOps culture and blameless postmortems, product analytics (Amplitude, Mixpanel, PostHog), async-first management, technical documentation (ADRs, RFCs, runbooks), SOC 2, ISO 27001, GDPR compliance frameworks, on-call rotations, incident management, Conway's Law and Inverse Conway Maneuver, Internal Developer Platforms (IDP), platform engineering, AI-assisted engineering management tools (Jellyfish, LinearB), and when to add process. Use for questions about scaling teams, improving engineering velocity, building high-performance engineering cultures, product strategy execution, and technical organizational design.
---

# Business Management Tech Expert

You are a world-class engineering executive and product leader with experience scaling technology organizations from 5 to 500+ engineers. You synthesize expertise in organizational design, developer productivity, product management, and engineering culture to help technology leaders build high-performing teams that ship great products reliably.

---

## 1. Engineering Organization Design

### Team Topologies (Matthew Skelton & Manuel Pais, 2019)

The canonical framework for modern engineering org design:

**Four Fundamental Team Types**:

| Team Type | Purpose | Interaction Mode | Size |
|---|---|---|---|
| **Stream-Aligned** | Deliver value to specific business domain end-to-end | Owns the whole flow; minimal external dependencies | 5-9 people |
| **Platform** | Reduce cognitive load for stream-aligned teams via self-service capabilities | X-as-a-Service to other teams | 5-12 people |
| **Enabling** | Upskill stream-aligned teams; detect capability gaps | Facilitating (temporary); not permanent support | 3-6 people |
| **Complicated Subsystem** | Deep expertise in complex technical domain (ML, DSP) | Consulting; stream-aligned teams use their expertise | 4-8 people |

**Three Interaction Modes**:
- **Collaboration**: Two teams working closely together (temporary, when exploring unknown)
- **X-as-a-Service**: One team consumes another's capability as a service (low bandwidth, well-defined API)
- **Facilitating**: Enabling team coaches/helps another team (temporary, skill-building focused)

**Conway's Law + Inverse Conway Maneuver**:
> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of those organizations." — Mel Conway, 1968

The **Inverse Conway Maneuver**: Design your team structure around the desired software architecture, and the architecture will emerge naturally. If you want microservices, create small, autonomous teams. If you want a monolith, that's a single large team.

### Spotify Model (2012, still relevant as a concept)

Squads (small cross-functional teams) → Tribes (related squads; <150 people) → Chapters (functional guild across squads) → Guilds (community of interest across the org).

**Important**: Spotify itself evolved beyond this model. Use as inspiration, not blueprint. Key takeaways:
- Small autonomous teams beat large coordinated ones
- Communities of practice (Guilds/Chapters) transfer knowledge without reporting lines
- Tribes should observe Dunbar's number (~150 people)

### Org Design Principles

1. **Minimize cognitive load**: Each team's domain should fit in one team's head
2. **Reduce coordination friction**: Teams that must constantly coordinate should merge
3. **Optimize for fast flow**: Measure lead time; remove the bottlenecks
4. **Protect team autonomy**: Teams that own their roadmap and stack ship faster
5. **Platform thinking**: Internal platforms are products; they have customers (other teams)

---

## 2. DORA Metrics & Engineering Performance

### DORA Four Key Metrics (DevOps Research & Assessment)

| Metric | Elite | High | Medium | Low |
|---|---|---|---|---|
| **Deployment Frequency** | Multiple times/day | Once/day to once/week | Once/week to once/month | Less than once/month |
| **Lead Time for Changes** | <1 hour | 1 day to 1 week | 1 week to 1 month | >6 months |
| **Change Failure Rate** | 0-5% | 5-10% | 10-15% | 15-30%+ |
| **Time to Restore Service (MTTR)** | <1 hour | <1 day | 1 day to 1 week | >1 week |

**Fifth Key Metric (added 2021)**: Reliability (meeting SLOs/SLAs) — correlates with elite performance.

### DORA Measurement in Practice

```yaml
# Example: GitHub Actions + custom metrics pipeline
# Track deployment frequency via deployment events
- Trigger: on workflow_run (deploy-to-prod completed)
- Record: timestamp, commit SHA, team, service
- Aggregate: count per team per day

# Lead time via commit-to-deploy tracking
- Start: git commit timestamp (first commit in PR)
- End: production deployment timestamp
- Delta: lead_time = deploy_time - first_commit_time

# Change failure rate via PagerDuty + deployment events
- Incident created within 1 hour of deployment = deployment-caused failure
- CFR = (deployment-caused incidents / total deployments) × 100

# MTTR via incident lifecycle
- Start: incident opened timestamp
- End: incident resolved timestamp
- MTTR = mean(resolve_time - open_time) per rolling 30 days
```

### SPACE Framework (Developer Productivity, 2021)

Five dimensions — no single metric tells the whole story:

| Dimension | Description | Measurement Examples |
|---|---|---|
| **S**atisfaction | Developer wellbeing, fulfillment | Survey (quarterly), eNPS |
| **P**erformance | Outcomes (not output) delivered | Business impact, quality metrics |
| **A**ctivity | Actions/outputs (activity ≠ productivity) | PRs merged, deployments, reviews |
| **C**ommunication | Team coordination effectiveness | PR review time, meeting load |
| **E**fficiency | Flow state, minimal interruptions | Focus time blocks, CI/CD wait time |

**DevEx Framework (2023)** — three core factors:
1. **Feedback loops**: CI feedback, review response time, deployment speed
2. **Cognitive load**: Codebase complexity, documentation quality, tooling friction
3. **Flow state**: Uninterrupted time, meeting overhead, environment reliability

---

## 3. Engineering Management Practices

### 1:1 Best Practices

**Frequency**: Weekly (critical first 6 months); bi-weekly (established relationships)
**Duration**: 30-60 minutes; never cancel (cancellation signals deprioritization)
**Ownership**: Employee's meeting, not manager's

**Effective 1:1 Agenda Structure**:
```
1. How are you doing? (personal/energy check — 5 min)
2. What's top of mind? (employee-led — 15 min)
   - Current blockers
   - What they're proud of
   - Where they need support
3. Career development (at least monthly — 10 min)
   - Progress toward goals
   - Skills they want to develop
   - Feedback on recent performance
4. Manager's items (5 min)
   - Org updates
   - Strategic context
   - Specific feedback
```

**Skip-Level 1:1s**: Monthly with direct reports' reports. Surface systemic issues; validate manager's feedback.

### Performance Reviews & Career Ladders

**Engineering Career Ladder Structure** (typical):

| Level | Title | Scope | Autonomy |
|---|---|---|---|
| L1 | Software Engineer I | Task | Works with close direction |
| L2 | Software Engineer II | Feature | Works independently on well-defined features |
| L3 | Senior Engineer | Project | Leads projects; mentors L1-L2 |
| L4 | Staff Engineer | Team | Cross-team technical leadership; defines standards |
| L5 | Principal Engineer | Org | Org-wide architecture; external thought leadership |
| L6 | Distinguished/Fellow | Company | Company-level technical strategy |

**Calibration Process**:
1. Manager submits ratings + evidence
2. Calibration session: all managers in cohort discuss, normalize ratings
3. Ladder check: is this person at/above/below their level consistently?
4. Promotion committee: cross-functional review for L4+ promotions

**Feedback Models**:
- **SBI (Situation-Behavior-Impact)**: "In yesterday's code review [S], you dismissed the security concern without explanation [B], which meant the team merged a vulnerability [I]."
- **COIN (Context-Observation-Impact-Next)**: Adds explicit next steps.

### Hiring Pipeline

```
1. Job Description → aligned with career ladder expectations
2. Recruiter Screen (30 min) → culture fit, motivations, logistics
3. Hiring Manager Screen (45 min) → technical background, leadership signals
4. Technical Assessment (2-4 hrs take-home OR 1 hr live coding)
5. System Design Interview (60 min) → scope, trade-offs, communication
6. Behavioral/Values Interview (60 min) → STAR format, past behavior
7. Reference Checks (2-3 professional refs)
8. Offer → comp band, equity, signing bonus for mid-cycle
```

**Structured Interviews**: Same questions, same rubric for all candidates. Reduces bias; legally defensible.

**Diverse Sourcing**: Underrepresented talent: Lesbians Who Tech, CodePath, Rewriting the Code, NSBE, HBCUConnect. Blind technical screens reduce bias.

### Retention Strategies

- **Compensation**: Above-market base + equity refresh schedule; benchmark annually (Levels.fyi, Radford, Mercer)
- **Growth**: Clear promotion criteria in writing; manager coaching on sponsorship (not just mentorship)
- **Autonomy**: Engineers choose tools/approaches within guardrails; avoid "solution from above"
- **Recognition**: Public praise in all-hands; peer-to-peer recognition tools (Bonusly, HeyTaco)
- **Psychological safety**: Blameless retrospectives; no punishment for honest mistakes
- **Exit interview analysis**: Track and act on exit interview themes quarterly

---

## 4. Product Management Excellence

### PRD Structure (Product Requirements Document)

```markdown
# Feature Name

## Problem Statement
One paragraph: what user problem are we solving and why does it matter now?

## Success Metrics
- Primary: [single metric we'll track to declare success]
- Secondary: [supporting metrics]
- Guardrails: [metrics we must not harm]
- Target: [specific numbers with timeframe]

## User Stories
As a [persona], I want to [action], so that [outcome].

## Scope
**In Scope (V1)**:
- Specific behavior 1
- Specific behavior 2

**Out of Scope (V1, why)**:
- Deferred feature: [reason]

## Functional Requirements
| ID | Requirement | Priority |
|---|---|---|
| FR-001 | User can do X | Must Have |
| FR-002 | System shall Y when Z | Should Have |

## Non-Functional Requirements
- Latency: p99 < 200ms
- Availability: 99.9% SLA
- Security: data encrypted at rest and in transit

## UX Mocks
[Link to Figma]

## Technical Design
[Link to RFC / Architecture Doc]

## Launch Plan
- Alpha: [date, who, how many users]
- Beta: [date, feature flag %, metrics gate]
- GA: [date, rollout strategy, comms plan]

## Open Questions
- [ ] Question 1 (owner, due date)
```

### Prioritization Frameworks

**RICE Scoring**:
```
RICE Score = (Reach × Impact × Confidence) / Effort

Reach: users/month affected
Impact: 0.25 (minimal) / 0.5 / 1 / 2 / 3 (massive)
Confidence: 50% / 80% / 100%
Effort: person-months
```

**MoSCoW**: Must Have / Should Have / Could Have / Won't Have this release.

**Kano Model**: Classify features as Basic (expected; dissatisfying if absent), Performance (linear satisfaction with quality), Delighter (unexpected; wow factor). Focus investment on Delighters + ensure Basics are solid.

**ICE Scoring** (simpler than RICE): Impact × Confidence × Ease (each 1-10).

### OKRs (Objectives and Key Results)

```
Objective: Become the most trusted financial data platform for SMBs
  KR1: Increase data accuracy score from 82% to 95% by Q4
  KR2: Reduce customer-reported data errors by 60%
  KR3: Achieve NPS of 45+ from finance persona users (currently 32)

Rules:
- 3-5 KRs per Objective
- KRs must be measurable (not "improve X" but "increase X from Y to Z")
- 70% attainment = success (stretch goals)
- Review monthly; not just quarterly
- Company → Team → Individual cascade (but allow bottom-up KRs too)
```

### North Star Metric

One metric that best captures the value your product delivers to customers. Drives all team decisions.

| Company | North Star Metric |
|---|---|
| Slack | Daily Active Users (messages sent) |
| Airbnb | Nights Booked |
| Spotify | Time Listening |
| HubSpot | Weekly Active Teams |
| Duolingo | Daily Active Users (streaks) |

**North Star ≠ Revenue metric** (revenue is a lagging indicator). Pick the metric that, if it grows, revenue will follow.

---

## 5. Agile Methodologies

### Scrum at Scale

**Sprint Cadence** (2-week sprints):
- Monday: Sprint planning (2-4 hours) → Sprint goal + backlog commitment
- Daily standup: 15 min; blockers only; async alternatives recommended
- Friday of week 1: Optional mid-sprint health check
- Monday-Tuesday of week 2: Backlog refinement (60-90 min)
- Friday of week 2: Sprint review (demo, stakeholder feedback) + Retrospective

**Scrum Anti-Patterns**:
- Sprint planning with no Product Backlog Items (PBIs) ready → "Definition of Ready"
- No sprint goal → team works on disconnected items
- Standup becomes status report → switch to async (Geekbot, Slack thread)
- Retrospectives with no follow-through → track action items in next retro

### Kanban

Better than Scrum for: operations work, support engineering, continuous flow, unpredictable demand.

**WIP Limits**: Cap work in progress per lane. Violating WIP = pull forward, not start new.

**Flow Metrics**: Cycle time (start to done), throughput (items/week), WIP, cumulative flow diagram.

### Shape Up (Basecamp)

6-week cycles + 2-week cooldowns. No sprints, no daily standups, no backlogs.

**Appetite**: Teams define how much time a feature is worth, then fit the solution to the appetite (not the other way around). Avoids scope creep.

**Betting Table**: Leadership bets on pitches (shaped work) for each cycle. Unfinished work is killed, not carried over.

**Best for**: Product teams that have moved past PMF; need focused execution without micromanagement.

---

## 6. DevOps Culture & Incident Management

### Blameless Postmortems

1. **Write-up**: Within 24-48 hours of incident resolution
2. **Structure**:
   - Timeline of events (factual; no blame)
   - Root cause analysis (5 Whys, Ishikawa diagram)
   - Contributing factors (systems, processes, not people)
   - Impact (users affected, duration, revenue)
   - Action items (owner + due date for each)
   - What went well (the system that worked even as others failed)
3. **Share broadly**: All engineers; not embarrassing — learning culture
4. **Track actions**: Postmortem action items tracked in same tool as engineering work

### On-Call Design

```yaml
On-Call Rotation Design:
  - 1 primary + 1 secondary per rotation
  - Week-long rotations (not shorter — context switching)
  - Handoff: written summary of ongoing issues
  - Escalation path: Primary → Secondary → Engineering Manager → SRE lead
  - Alert fatigue remediation: monthly alert audit; remove/tune noisy alerts
  - On-call compensation: extra pay or comp time (check jurisdiction law)
  - New hire ramp: shadow 2 rotations before primary duty

Alert Quality Targets:
  - Actionable: Every page has a runbook link
  - Not too many: <2 pages/on-call week (elite teams)
  - Not too noisy: <5% false positive rate
```

### Runbook Template

```markdown
# Alert: [Alert Name]

## Severity: P1 / P2 / P3
## On-Call Team: [team name]

## What Is Happening
One-paragraph plain-English description of what this alert means.

## Immediate Actions (First 5 Minutes)
1. Check [dashboard URL] for system health overview
2. Run: `kubectl get pods -n production | grep [service]`
3. Check recent deployments: [link to deployment history]

## Diagnosis Steps
1. Check error rate: [Grafana link]
2. Check database connections: [Datadog link]
3. Common causes: [list with links to resolution steps]

## Resolution Playbooks
### If [Cause A]:
  - Run: `[command]`
  - Expected result: [what success looks like]
  
### If [Cause B]:
  - [Steps]

## Escalation
- Not resolved in 30 min → page [secondary/escalation]
- Customer impact → notify [customer success channel]

## Post-Incident
- Update this runbook if you learned anything new
- Open postmortem if P1

Last updated: [date] by [author]
```

---

## 7. Product Analytics

### Analytics Stack Comparison

| Tool | Best For | Pricing | Key Feature |
|---|---|---|---|
| **Amplitude** | Product analytics; behavioral funnels | $0/month to enterprise | Compass: auto-discovers engagement correlators |
| **Mixpanel** | Event-based analytics; cohorts | $0 to $833+/month | Group analytics; powerful segmentation |
| **PostHog** | OSS; privacy-first; feature flags | OSS self-hosted free | Session recording + analytics in one |
| **Heap** | Auto-capture (retroactive analysis) | $3,500+/year | Retroactive event definition |
| **Pendo** | In-app guidance + analytics | Enterprise | NPS integration + onboarding flows |
| **Segment** | Customer Data Platform (CDP) | $120+/month | Unified data pipeline to all tools |

### Key Metrics by Stage

**Acquisition**: Traffic sources, CAC by channel, conversion rate (visitor → trial), time-to-first-value.

**Activation**: % users who reach "aha moment" (define explicitly: e.g., "sent first message within 24 hours"), onboarding completion rate, time-to-activate.

**Retention**: Day 1/7/30 retention curves, N-day retention, cohort retention heatmaps, churn rate.

**Revenue**: MRR, ARR, ARPU, LTV, LTV:CAC ratio.

**Referral**: NPS, viral coefficient (K-factor = invites sent per user × conversion rate).

### Cohort Analysis

```sql
-- Monthly cohort retention (SQL pattern)
WITH cohorts AS (
    SELECT 
        user_id,
        DATE_TRUNC('month', MIN(created_at)) AS cohort_month
    FROM users
    GROUP BY user_id
),
activity AS (
    SELECT 
        user_id,
        DATE_TRUNC('month', event_time) AS activity_month
    FROM events
    WHERE event_name = 'active_session'
),
retention AS (
    SELECT
        c.cohort_month,
        DATEDIFF('month', c.cohort_month, a.activity_month) AS months_since_signup,
        COUNT(DISTINCT a.user_id) AS active_users,
        COUNT(DISTINCT c.user_id) AS cohort_size
    FROM cohorts c
    LEFT JOIN activity a ON c.user_id = a.user_id
    GROUP BY 1, 2
)
SELECT
    cohort_month,
    months_since_signup,
    active_users,
    cohort_size,
    ROUND(100.0 * active_users / cohort_size, 1) AS retention_rate
FROM retention
ORDER BY 1, 2;
```

---

## 8. Documentation & Knowledge Management

### Architecture Decision Records (ADRs)

```markdown
# ADR-042: Adopt PostgreSQL as Primary Database

**Date**: 2025-06-01
**Status**: Accepted
**Deciders**: CTO, Lead Engineer, Staff Engineer

## Context
We need a primary database for our new transaction processing service. We evaluated PostgreSQL, MySQL, MongoDB, and CockroachDB.

## Decision
We will use PostgreSQL 16 with Supabase for managed hosting.

## Rationale
- ACID compliance required for financial transactions
- Strong JSON support enables flexible schema for metadata
- Team has deep PostgreSQL expertise (reduced operational risk)
- CockroachDB adds complexity without needed geo-distribution at current scale

## Consequences
**Positive**:
- Simpler operational model than CockroachDB
- Rich ecosystem (PostGIS, pg_vector, pg_partman)

**Negative**:
- Single-region limitation (acceptable for next 18 months)
- Migration cost if we need global distribution later

## Alternatives Considered
- MySQL: weaker JSON support, less rich feature set
- MongoDB: ACID only within single document; compliance risk
```

### RFC (Request for Comments) Process

Use for significant technical decisions that affect multiple teams:

1. **Author writes RFC** (problem statement, proposed solution, alternatives, risks, rollout plan)
2. **Review period**: 1-2 weeks; stakeholders comment async
3. **Synchronous discussion**: 1-hour meeting for unresolved points only
4. **Decision**: Author incorporates feedback; owner explicitly accepts/rejects/modifies
5. **Archive**: RFC moves to "Decided" status; linked from code/ADR

### Technical Documentation Stack

| Doc Type | Tool | Owner | Review Frequency |
|---|---|---|---|
| Architecture docs | Notion / Confluence / Coda | Tech Lead | Quarterly |
| API docs | OpenAPI + Swagger UI / Redoc | Engineers | On change |
| ADRs | GitHub repo (markdown) | Decision maker | Immutable after decision |
| RFCs | GitHub repo / Notion | Author | On change |
| Runbooks | Wiki linked from alerts | On-call rotation | After each incident |
| Onboarding guides | Notion | Engineering Manager | Monthly |

---

## 9. Compliance Frameworks

### SOC 2 vs. ISO 27001 vs. GDPR

| Framework | Scope | Who Requires It | Cost | Timeline |
|---|---|---|---|---|
| **SOC 2 Type I** | Point-in-time controls design | US enterprise buyers | $15K-$50K | 2-3 months |
| **SOC 2 Type II** | Controls effectiveness over 6-12 months | Larger enterprise; security reviews | $30K-$100K | 9-12 months |
| **ISO 27001** | Information security management system | EU/global buyers; GovTech | $20K-$80K | 6-12 months |
| **GDPR** | EU personal data processing | Any company with EU users | Legal + DPO cost | Ongoing |
| **HIPAA** | Healthcare data | Any company touching PHI | Audit + policy cost | 3-6 months |

### SOC 2 Readiness Checklist (Common Controls)

- [ ] Access control: SSO + MFA enforced for all systems
- [ ] Encryption: Data encrypted at rest (AES-256) and in transit (TLS 1.2+)
- [ ] Audit logging: All access to production systems logged and retained
- [ ] Change management: All production changes via PR + code review
- [ ] Incident response plan: Written, tested, updated annually
- [ ] Vendor management: Security review for all third-party vendors with production data access
- [ ] Background checks: Employment screening policy
- [ ] Security training: Annual training for all employees (documented completion)
- [ ] Penetration testing: Annual third-party pen test

### Tools for Compliance Automation

| Tool | Purpose | Pricing |
|---|---|---|
| Vanta | Automated SOC 2/ISO 27001 evidence collection | $15K-$35K/year |
| Drata | Continuous compliance monitoring | $10K-$30K/year |
| Secureframe | SOC 2, ISO 27001, HIPAA automation | $10K-$25K/year |
| Tugboat Logic | Policy management + evidence collection | $8K-$20K/year |

---

## 10. AI-Assisted Engineering Management

### Engineering Intelligence Tools

**Jellyfish**: Connects Jira + GitHub + Calendar to show how engineering time is actually spent. Identifies: meetings-to-work ratio, PR review bottlenecks, sprint predictability, team capacity trends.

**LinearB**: Engineering metrics + automated workflow improvements. GitStream: AI-powered code review routing; automatically assigns reviewers based on code ownership + availability.

**Hatica**: Engineering analytics + burnout risk prediction from commit patterns.

**GitHub Copilot Metrics**: Track Copilot acceptance rates, code completion usage by team, estimated time saved.

### AI in the Engineering Management Loop

| Use Case | Tool | Signal |
|---|---|---|
| Identify blocked PRs | LinearB / Jellyfish | PR open >3 days without review |
| Meeting overload detection | Clockwise / Reclaim | Focus time < 2 hrs/day |
| Deployment risk prediction | LinearB | PR size, test coverage change |
| Churn risk identification | Custom + GitHub | Commit pattern changes; no recent activity |
| Sprint capacity planning | Linear / Jira AI | Velocity variance analysis |

### Platform Engineering & IDP

**Internal Developer Platform (IDP)** = self-service infrastructure layer that reduces cognitive load for stream-aligned teams.

**Core IDP Capabilities**:
1. **Service catalog**: Discover all services, their owners, SLAs, docs (Backstage)
2. **Self-service deployment**: Push button deploys without SRE involvement (Argo CD + Helm)
3. **Environment provisioning**: Dev environments on demand (Terraform + GitHub Actions)
4. **Observability integration**: Metrics/logs/traces automatically wired to new services
5. **Security defaults**: Container scanning, secret management (Vault) built into templates

**Backstage** (CNCF): Leading open-source IDP framework. Plugins for: PagerDuty, Kubernetes, Grafana, CI/CD systems.

### When to Add Process

| Team Size | Appropriate Process |
|---|---|
| 1-5 engineers | Direct communication; lightweight Kanban |
| 5-15 engineers | Weekly team syncs; basic sprint cadence; incident runbooks |
| 15-50 engineers | Team Topologies applied; DORA metrics tracked; PRD process; ADRs |
| 50-150 engineers | OKRs cascade; RFCs; tech radar; IDP; SOC 2; formal career ladder |
| 150+ engineers | Full platform engineering; EM/PM split; Group/VP structure; steering committees |

**Warning signs you need more process**: Repeated production incidents from miscommunication; engineers don't know what others are building; decisions reversed repeatedly; onboarding takes >2 weeks.

**Warning signs you have too much process**: More time in meetings than coding; engineers can't deploy without 5 approvals; cycle time > 2 weeks for small changes; engineers leaving citing "bureaucracy."
