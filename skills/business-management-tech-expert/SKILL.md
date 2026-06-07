---
name: business-management-tech-expert
description: Expert-level reference for engineering managers, VPs of Engineering, and CTOs on scaling technology organizations in 2025–2026. Use when asked about Conway's Law, Team Topologies, org design, engineering management, DORA metrics, OKRs, tech radar, SOC 2, board management, or M&A integration.
---

# Business Management Tech Expert

## 1. Engineering Organization Design

### Conway's Law and Inverse Conway Maneuver

**Conway's Law (1967, Melvin Conway):**
> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of those organizations."

This means your software architecture mirrors your org structure. Use the **Inverse Conway Maneuver**: design your target architecture first, then reorganize teams to match it.

**Practical application:**
```
BAD: Large monolith → 20-person team owns it all → communication bottlenecks → slow deploys

GOOD: Decide on microservices/domain boundaries first → 
      Structure teams around those domains → 
      Each team owns + deploys their service independently
```

### Team Topologies (Skelton & Pais, 2019)

Four fundamental team types:

| Team Type | Primary Goal | Interaction Mode |
|---|---|---|
| **Stream-aligned** | Deliver value to end users in a flow | Direct collaboration |
| **Platform** | Reduce cognitive load of stream-aligned teams | X-as-a-Service |
| **Enabling** | Fill skill gaps, enable stream teams | Collaboration (time-limited) |
| **Complicated-subsystem** | Manage complexity requiring specialist knowledge | X-as-a-Service |

**Three interaction modes:**
1. **Collaboration**: high bandwidth, time-limited (discovery, prototyping)
2. **X-as-a-Service**: low bandwidth, stable API contract
3. **Facilitating**: enabling team supports stream team for a sprint, then withdraws

**Team topology for 50-person engineering org:**
```
Stream-aligned teams (own features end-to-end):
  → Product Team A (user acquisition)
  → Product Team B (core product)  
  → Product Team C (monetization)

Platform team (internal developer experience):
  → CI/CD, infrastructure, observability platform
  → Consumed as-a-service by product teams

Enabling team (temporary):
  → Security guild: upskills product teams on OWASP, then withdraws
  → AI integration: enables teams to ship AI features, then withdraws

Complicated subsystem:
  → ML/AI team: maintains models, exposes prediction API
```

### Cognitive Load as the Primary Design Constraint

Teams have a **cognitive load limit**. When exceeded:
- Quality decreases
- Velocity drops
- Burnout increases
- Knowledge hoarding emerges

**Team cognitive load sizing:**
- Max ~8 people per team (Amazon's two-pizza rule)
- Each team should own a domain they can fully understand
- Minimize the number of codebases / services a team must touch
- Kill old systems that require cognitive load without delivering value

---

## 2. Engineering Metrics (DORA)

The **DORA metrics** (DevOps Research and Assessment) are the industry standard for measuring software delivery performance.

### Four Key Metrics

| Metric | Elite | High | Medium | Low |
|---|---|---|---|---|
| **Deployment Frequency** | On-demand (multiple/day) | Weekly-monthly | Monthly-6mo | <6 months |
| **Lead Time for Changes** | <1 hour | 1 day-1 week | 1 week-1 month | >6 months |
| **Change Failure Rate** | 0–5% | 5–10% | 10–15% | 15–20%+ |
| **Time to Restore Service** | <1 hour | <1 day | 1 day-1 week | >6 months |

**Measuring DORA in practice:**
```python
# Lead time: commit → production
lead_time_seconds = deployment_timestamp - first_commit_timestamp

# Deployment frequency: deploys per day/week
deployment_frequency = total_deployments / time_period_days

# Change failure rate: % deploys causing incidents
change_failure_rate = incidents_caused_by_deploys / total_deploys

# MTTR: time from incident detection to resolution
mttr_seconds = incident_resolved_timestamp - incident_detected_timestamp
```

**Tools for DORA measurement:**
- LinearB: automated DORA from Git + Jira/Linear
- Sleuth: deployment tracking + DORA dashboard
- GitHub Actions + custom scripts
- Google Cloud Deploy: built-in DORA metrics (GCP native)

### Accelerate Research Findings (Forsgren, Humble, Kim)

- Elite performers are **1,460× more likely to deploy on-demand** than low performers
- Elite performers recover from incidents in **1/168th the time** of low performers
- **Both speed and stability improve together** — trade-offs are a myth for high performers
- The biggest predictor of elite performance: **loosely coupled architecture** (not process)

---

## 3. Engineering Management

### The Engineering Manager's Core Responsibilities

**Tactical (weekly):**
- 1:1s with all direct reports (30–60 min/week per person)
- Sprint planning participation
- Unblocking teams from cross-team dependencies
- Code review culture and quality oversight

**Strategic (quarterly):**
- Career growth planning for each engineer
- Technical roadmap input
- Hiring plan execution
- Cross-functional relationship management

**1:1 framework (recurring):**
```
Status updates: 10% (save async for this)
Growth conversation: 30%
Unblocking: 30%
Feedback exchange: 30%

Questions that work:
- "What's something I should know that I don't?"
- "What would make your work easier?"
- "Where do you want to be in 2 years?"
- "What's frustrating you right now?"
- "How can I be a better manager for you?"
```

### Engineering Levels Framework (IC Track)

```
L1 (Junior): Executes well-defined tasks, needs guidance
L2 (Mid): Independently ships features, good judgment on defined problems
L3 (Senior): Owns and leads complex projects, mentors L1/L2
L4 (Staff): Sets technical direction for a team or domain
L5 (Principal): Cross-team/org technical strategy
L6 (Distinguished/Fellow): Company-wide technical vision, industry leadership
```

**Promotion criteria framework:**
- **Scope**: How large is the problem space this person navigates?
- **Autonomy**: How much supervision is required?
- **Influence**: How many people/teams does this person's work affect?
- **Craft**: Technical depth and quality of output
- **Leadership**: Elevating others (not just personal output)

### Performance Management

**PIPs (Performance Improvement Plans):**
- Use only after extensive coaching and documentation
- Set specific, measurable, time-bound goals (60–90 days)
- Involve HR before initiating
- Document every conversation

**High performer recognition:**
- Promotion timing: don't wait — promote when the bar is clearly met
- Visibility: ensure leadership knows about their wins
- Interesting work: best retention is challenging projects
- Compensation: regular market corrections (not annual "surprise" underpayments)

---

## 4. OKRs (Objectives and Key Results)

### OKR Design Principles

**Good OKR criteria:**
- Objective: qualitative, inspiring, memorable
- Key Result: measurable, time-bound, outcome-oriented (not activity-based)
- Ambitious: 70% achievement = success (not 100% = failure)
- Aligned: supports company/team OKRs above

**Bad OKRs (too common):**
```
BAD KR: "Ship the authentication feature" (activity, not outcome)
GOOD KR: "95% of users complete signup in <60 seconds"

BAD KR: "Improve code quality" (vague, unmeasurable)
GOOD KR: "Reduce P1 incident rate from 4/month to 1/month"

BAD KR: "Have 10 technical design docs reviewed" (process metric)
GOOD KR: "Zero architecture surprises in production (measured by post-mortems)"
```

**Engineering team OKR example:**
```
Objective: Make our platform so reliable that customers brag about it

KR1: Achieve 99.9% API availability (from current 99.3%)
KR2: P99 API latency drops from 800ms to 200ms
KR3: Mean time to resolve (MTTR) drops from 4 hours to 1 hour
KR4: 0 repeat incident causes (each incident pattern caught once)
```

### OKR Cadence

```
Annual: Company-level OKRs set by leadership
Q planning: Team OKRs drafted (bottom-up), aligned to company
Monthly: Progress check, blockers surfaced
Weekly: Key results updated in tracking tool (Linear, Notion, Lattice)
Quarterly review: Grade KRs (0.0–1.0), learn, improve process
```

---

## 5. Technology Radar and Technical Strategy

### ThoughtWorks Technology Radar Model

Classify technologies into:
- **Adopt**: strong recommendation for broad use
- **Trial**: worth trying in low-risk projects
- **Assess**: worth monitoring, but not yet proven
- **Hold**: proceed with caution or stop using

**Building an internal tech radar:**
```
Quadrants: Languages/Frameworks | Tools | Platforms | Techniques

Process:
1. Engineering all-hands: propose additions/changes
2. Tech leads vote (Adopt/Trial/Assess/Hold)
3. CTO/Principal engineers adjudicate
4. Publish quarterly
5. All new projects use Adopt-level tech by default
6. Exceptions: documented and approved

Key questions:
- Is this actively maintained and secure?
- Do we have expertise or hiring pipeline for this?
- What's the migration path if it fails?
- Does it have the scale characteristics we need?
```

### Technical Debt Management

**Technical debt quadrants (Fowler):**
```
              RECKLESS         PRUDENT
DELIBERATE:  "We don't have    "We must ship now,
              time for design"  will fix later"

INADVERTENT: "What's layering?" "Now we know how
                                 we should have done it"
```

**Managing debt systematically:**
```python
# Debt tracking in Linear/Jira
debt_categories = {
    "architecture": "Fundamental design limitations",
    "test_coverage": "Missing/inadequate tests",
    "dependency": "Outdated/insecure dependencies",
    "documentation": "Missing knowledge transfer material",
    "observability": "Missing metrics, traces, logs",
}

# Debt budget approach:
# Allocate 20% of each sprint to debt reduction
# Track debt items as first-class tickets
# Never let debt accumulate without scheduled resolution
```

---

## 6. SOC 2 Compliance

### SOC 2 Overview

SOC 2 (System and Organization Controls 2) is an auditing standard developed by the AICPA. Critical for selling to enterprise customers.

**Trust Service Criteria:**
1. **Security** (required) — protection against unauthorized access
2. **Availability** — system available as committed
3. **Processing Integrity** — complete, accurate, timely processing
4. **Confidentiality** — information designated as confidential is protected
5. **Privacy** — personal information handled per privacy notice

**Type I vs. Type II:**
- **Type I**: point-in-time assessment (design of controls only)
- **Type II**: period-in-time audit (usually 6–12 months; verifies controls operate effectively)
- Enterprise customers typically require **Type II**

### SOC 2 Preparation Timeline

```
Month 1-2: Readiness Assessment
  - Identify scope (systems, services)
  - Gap analysis against SOC 2 criteria
  - Hire compliance platform (Vanta, Drata, Secureframe)

Month 2-4: Control Implementation
  - Access control policies and procedures
  - Incident response plan
  - Vulnerability management program
  - Change management process
  - Vendor risk management
  - Employee security training

Month 4-6: Type I Audit
  - Auditor selected
  - Evidence collection
  - Type I report issued

Month 6-18: Type II Observation Period
  - Controls operating throughout
  - Continuous evidence collection via automation

Month 18-24: Type II Report Issued
```

**Key controls for software companies:**
```
Access Management:
  ✓ MFA on all critical systems
  ✓ Least privilege access (quarterly reviews)
  ✓ Offboarding checklist (<24h access revocation)
  ✓ Privileged access management (PAM)

Software Development:
  ✓ Code review required (no self-merge to main)
  ✓ Vulnerability scanning in CI/CD (Snyk, Semgrep)
  ✓ SBOM (Software Bill of Materials)
  ✓ Penetration testing (annual)

Infrastructure:
  ✓ Encryption at rest and in transit
  ✓ Logging and monitoring (CloudTrail, etc.)
  ✓ Backup and disaster recovery tested
  ✓ Configuration management (no drift)
```

**Compliance automation tools:** Vanta (most popular for startups), Drata, Secureframe, Tugboat Logic.

---

## 7. Scaling the Engineering Organization

### Hiring and Team Building

**Hiring funnel metrics:**
```
Stage          Pass Rate    Target Pass Rate
Sourcing       100%         —
Recruiter Screen   20%     20–25%
Technical Screen   50%     40–60%
Onsite/Loop        50%     30–50%
Offer              80%     75–85%
Accept             75%     60–80%

Optimal: 1 hire per ~25 sourced candidates
```

**Structured interview process:**
1. Recruiter screen: motivation, logistics, compensation alignment
2. Technical screen (45 min): LC-style problem + communication
3. System design (60 min): design distributed system at scale
4. Coding (60 min): real-world problem, not algo puzzles
5. Values/culture (30 min): past behavior, team fit
6. Hiring manager: final alignment

**Ramp-up program (90 days):**
```
Week 1: Environment setup, architecture deep-dive, shadowing
Week 2-4: First small, self-contained ticket ("starter bug")
Month 2: Owns a small feature end-to-end
Month 3: Contributing to planning, reviewing others' code
90-day review: calibrate against role expectations
```

### Remote and Distributed Engineering

**Async-first engineering culture:**
```
Sync: Used for high-bandwidth decisions and relationship building
Async: Default for everything else

Required async artifacts:
  - Technical Design Document (TDD) before major features
  - Decision records (ADR) for architecture choices
  - Weekly written team update (replaces status meetings)
  - Sprint demo video instead of live demo meeting

Sync kept:
  - 1:1s (relationship building)
  - Incident war rooms
  - Quarterly planning alignment
  - Design review (early stage, ambiguous problems)
```

---

## 8. M&A Integration for Tech Companies

### Technical Due Diligence Checklist (Acquirer Perspective)

**Architecture and quality:**
- [ ] Code repository access: size, age, primary languages
- [ ] Automated test coverage (unit, integration, E2E)
- [ ] CI/CD pipeline maturity and deployment frequency
- [ ] Incident history and MTTR
- [ ] Technical debt inventory

**Security:**
- [ ] Penetration test results (last 12 months)
- [ ] SOC 2 / ISO 27001 / FedRAMP status
- [ ] Known CVEs in dependencies
- [ ] Access control and identity management
- [ ] Data classification and encryption

**Infrastructure:**
- [ ] Cloud spend and optimization
- [ ] Vendor contracts and lock-in
- [ ] SLA commitments to customers
- [ ] Disaster recovery and backup procedures

**IP:**
- [ ] All code covered by employee/contractor agreements
- [ ] Open source license inventory
- [ ] Patent filings and status
- [ ] Key person dependency analysis

### Post-Acquisition Integration Phases

```
Phase 1 (Day 0–90): Stabilize
  - Keep systems running; do not merge
  - Integrate identity/access management
  - Consolidate communication (Slack, email)
  - Protect key talent (retention packages)

Phase 2 (Month 3–9): Integrate
  - Technical integration planning (API, data)
  - Merge overlapping tools (choose one, deprecate other)
  - Align engineering processes (code review, deploy process)
  - Cultural integration programs

Phase 3 (Month 9–18): Optimize
  - Full tech stack consolidation (if decided)
  - Eliminate duplicate infra
  - Extract synergies
  - Full team integration to parent structure
```

---

## 9. Board Management for Technical Founders

### Engineering/CTO Board Reporting

**What VCs care about from engineering:**
1. **Velocity**: are we shipping faster or slower over time?
2. **Quality**: incident rate, bug count, customer-reported issues
3. **Scalability**: can the system support 10x growth without full rewrite?
4. **Security/compliance**: are we SOC 2, GDPR compliant?
5. **Team health**: attrition, hiring pipeline, key person dependencies

**Board metrics dashboard:**
```
Engineering Health (quarterly):
  DORA Metrics:
    - Deployment frequency: N/week
    - Lead time: X hours
    - Change failure rate: Y%
    - MTTR: Z hours
  
  Quality:
    - P1 incidents: N (vs. prior quarter)
    - Customer-reported bugs: N
    - Test coverage: N%
  
  Team:
    - Engineering headcount: N (vs. plan)
    - Voluntary attrition: N%
    - Open requisitions: N
    - Time-to-hire (engineering): N days
  
  Scalability:
    - p99 API latency: Nms (vs. SLA)
    - Infrastructure cost per customer: $N
    - Capacity: current system supports Nx current load
```

---

## 10. Engineering Culture and DEIA

### Building an Inclusive Engineering Culture

**Structural inclusion practices:**
- Blind resume review (remove names, universities)
- Standardized interview rubrics (reduces interviewer bias)
- Diverse interview panels
- Salary bands by level (no negotiation-based pay disparities)
- Promotion criteria published (reduces "sponsor-based" advancement)

**Psychological safety (Google Project Aristotle finding #1):**
- Leaders model vulnerability and intellectual humility
- Post-mortems are blameless (focus on system, not people)
- "Bad news fast" culture: surface problems early without fear
- Dissent is explicitly valued: "the most senior person should speak last"

**Common cultural anti-patterns to avoid:**
```
ANTI-PATTERN: "Meritocracy" claim without examining process biases
ANTI-PATTERN: All-hands meeting where only 5 people actually speak
ANTI-PATTERN: "Culture fit" as rejection reason (often means "like me")
ANTI-PATTERN: No promotion criteria published → sponsorship-based advancement
ANTI-PATTERN: On-call rota that burns out junior engineers disproportionately
```

### Engineering Manager's Playbook for Difficult Situations

**Common scenarios:**

**Underperformer:**
1. Document feedback given in 1:1s
2. Set explicit expectation in writing
3. 30-day informal improvement conversation
4. If no change: formal PIP with HR
5. Separation with proper notice if PIP fails

**Team conflict:**
1. Talk to each party separately first
2. Identify root cause (work style? unclear ownership? past incident?)
3. Mediate direct conversation
4. If structural: change team structure, clear ownership boundaries
5. If interpersonal: consider team restructure

**Star performer threatening to leave:**
1. Have the real conversation: what would make this work?
2. Address root cause (compensation? scope? management?)
3. Don't make counter-offers if culture is the issue
4. Document knowledge transfer regardless
