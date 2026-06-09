---
name: business-management-tech-expert
description: Expert-level technology business management knowledge covering engineering team leadership (1:1s, performance reviews, engineering ladders, psychological safety), product management (RICE/ICE/MoSCoW prioritization, OKRs, NSM), organizational design (Conway's Law, Team Topologies, Inverse Conway Maneuver), hiring and equity compensation, technical culture (code review, blameless postmortems, on-call SLIs/SLOs), Agile methodologies (Scrum, Kanban, Shape Up), remote team management, CTO vs. VP Engineering, scaling phases, and AI team management. Use for any engineering management, product strategy, organizational design, or tech leadership question.
---

# Business Management Tech Expert

## Engineering Leadership Fundamentals

### The 1:1 Framework

1:1s are the single most important tool for engineering managers. Non-negotiable weekly cadence for all direct reports.

**Structure (30–45 min):**
- First 10 min: Their agenda—what they want to discuss (NOT a status update)
- Next 15 min: Coaching, unblocking, context-sharing
- Last 10 min: Manager's agenda—feedback, strategic context, upcoming changes

**Key principles:**
- The 1:1 is **for them**, not for you. Status updates belong in async tools (Slack, Jira, Linear)
- Prepare in a shared doc; both parties add items asynchronously between meetings
- Track mood/energy signals over time—declining engagement precedes departure by 4–6 weeks
- Psychological contract: consistent, canceled-only-as-last-resort

**Topics to cover across the month:**
- Career development and growth goals (at least monthly)
- Feedback (both directions—ask for feedback on your own management)
- Blockers and frustrations
- Team dynamics and cross-team relationships
- Recognition and accomplishment acknowledgment

### Engineering Ladders

Clear career levels create fairness, retention, and hiring calibration. Standard SWE ladder:

| Level | Title | Scope | Experience |
|-------|-------|-------|-----------|
| L3 | Software Engineer | Individual tasks; needs guidance | 0–2 years |
| L4 | Software Engineer II | Features; limited guidance | 2–5 years |
| L5 | Senior Software Engineer | Complex features; mentors L3/L4 | 5–8 years |
| L6 | Staff Engineer | Cross-team impact; technical strategy | 8–12 years |
| L7 | Principal Engineer | Org-wide impact; define architecture | 12+ years |
| L8 | Distinguished/Fellow | Industry-level impact; rare | N/A |

**Level dimensions:** Technical Skills, Execution, Collaboration, Communication, Scope of Impact. Define specific, observable behaviors for each dimension at each level.

**Promotion criteria:** Sustained performance at NEXT level for 2–3 quarters, not just meeting current level. Promotion should be unsurprising—the candidate should already be operating at the level before being promoted.

**IC vs. management track:** Most companies bifurcate at Senior (L5)—separate Staff Engineer track and Engineering Manager track with equivalent compensation. Forcing strong engineers into management is a retention mistake.

### Performance Reviews

**Frequency:** Annual formal review + mid-year calibration check-in + quarterly goal reviews.

**Calibration process:**
1. Employee writes self-review against their level's competencies
2. Manager writes review with specific examples
3. Cross-manager calibration session: "Forced ranking" or distribution review (top 10%, core 80%, underperforming 10%)
4. Manager delivers feedback with compensation decision

**Effective feedback structure (SBI model):**
- **Situation:** "In the Q3 planning meeting..."
- **Behavior:** "...you interrupted three colleagues before they finished their thoughts..."
- **Impact:** "...which caused two of them to stop contributing ideas. The team missed important input."

**Avoiding bias:**
- Recency bias: Review the full year, not just the last quarter
- Halo effect: One strong trait doesn't mean all traits are strong
- Attribution bias: Internal attribution for your team's successes; external for failures (flip to challenge it)
- Similarity bias: Don't systematically rate people like you higher

### PIP (Performance Improvement Plan)

A PIP should be a genuine last-resort tool, not a paper trail for termination. Effective PIPs:

1. Document specific, measurable behavioral gaps with examples
2. Set clear, achievable goals with timeframes (30/60/90 days)
3. Define what "success" looks like explicitly
4. Provide necessary support (coaching, training, clearer expectations)
5. Weekly check-ins during PIP period
6. If performance improves, exit PIP explicitly with acknowledgment
7. If not improved, termination with documented cause

**Warning:** In many states (California), PIPs before termination are NOT legally required but can actually create liability if the documented standard seems arbitrary or the process was followed inconsistently across employees.

---

## Product Management

### Prioritization Frameworks

**RICE Scoring:**
```
RICE Score = (Reach × Impact × Confidence) / Effort

Reach: How many users affected per period (users/quarter)
Impact: Magnitude of effect per user (0.25=minimal, 0.5=low, 1=medium, 2=high, 3=massive)
Confidence: % confidence in estimates (80-100% = well-defined, 50-80% = likely, <50% = uncertain)
Effort: Total person-months required

Example: Feature with Reach=500, Impact=2, Confidence=80%, Effort=3 months
RICE = (500 × 2 × 0.80) / 3 = 266.7
```

**ICE Scoring** (faster, less granular):
```
ICE = Impact × Confidence × Ease
Each scored 1-10. Sort descending. Good for early-stage product decisions.
```

**MoSCoW:**
- **Must Have:** Non-negotiable for this release (ship/don't-ship criteria)
- **Should Have:** High value, not critical for launch; include if possible
- **Could Have:** Nice to have; include if effort is trivial
- **Won't Have:** Explicitly out of scope for this iteration

**Kano Model:** Classifies features by user satisfaction curve:
- Basic needs (expected, must have; absence causes dissatisfaction)
- Performance features (more = better; proportional to satisfaction)
- Delight/excitement features (unexpected; creates "wow"; becomes basic need over time)

### OKRs (Objectives and Key Results)

**Structure:**
```
Objective: Qualitative, inspiring, directional statement of what to achieve
Key Results (3-5 per O): Quantitative, measurable outcomes that define success

Example:
  Objective: Become the fastest onboarding experience in our market
  
  KR1: Reduce time-to-first-value from 47 minutes to 15 minutes (measured by event analytics)
  KR2: Increase Day-7 activation rate from 32% to 55%
  KR3: Achieve 90+ NPS score for the onboarding flow (currently 71)
  KR4: 80% of new users complete onboarding without contacting support
```

**John Doerr's four OKR superpowers:** Focus and commit, align and connect, track for accountability, stretch for amazing.

**OKR scoring:** 0.0–1.0 per KR; 0.7 target (70% achievement of ambitious goals is success; 1.0 means goal was too easy). Never use OKRs for performance reviews directly—creates sandbagging.

**Common mistakes:**
- Too many OKRs (max 3–5 per team per quarter)
- Key Results are outputs (tasks), not outcomes (results)
- Individual OKRs instead of team OKRs (team OKRs drive collaboration)
- Set-and-forget (requires weekly check-ins and monthly updates)

### North Star Metric (NSM)

A single metric that best captures the core value your product delivers to customers—a leading indicator of long-term business success.

**Examples:**
- Airbnb: Nights booked (both supply and demand represented)
- Slack: Daily active users who send ≥2 messages/week
- Spotify: Time spent listening per day
- GitHub: Repositories with ≥1 contributor active this week
- Stripe: Total payment volume processed

**NSM vs. revenue metric:** Revenue is a lagging indicator. NSM should predict future revenue while measuring the value customers actually receive.

**Discovery process:**
1. List your most engaged users' behaviors
2. What action best correlates with long-term retention?
3. What action is a proxy for the value delivered?
4. Can you measure it reliably?

---

## Organizational Design

### Conway's Law and the Inverse Conway Maneuver

**Conway's Law (1967):** "Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations."

**Practical implication:** Your software architecture will mirror your org structure—not by choice, but by gravitational force. Monoliths come from big centralized teams; microservices come from small, independent teams.

**Inverse Conway Maneuver:** Deliberately structure your organization to match the desired system architecture:
- Want microservices? Create small, independent teams owning end-to-end services
- Want a platform? Create a dedicated platform team that others consume as internal customers
- Want modularity? Give each team ownership of a bounded context

### Team Topologies

From the book *Team Topologies* (Skelton & Pais, 2019). Four fundamental team types:

**1. Stream-Aligned Team**
- Aligned to a flow of work from a business domain (e.g., "checkout," "notifications," "billing")
- Owns the full stack of their domain—frontend, backend, data, infrastructure
- Goal: End-to-end feature delivery with minimum dependency on others
- Size: 5–9 people (Dunbar's number subsystem)
- Highest ratio of team type in the org

**2. Platform Team**
- Provides internal product/service that stream-aligned teams build upon
- Reduces cognitive load on stream-aligned teams
- Examples: Developer experience, CI/CD platform, infrastructure platform, data platform
- Treats stream-aligned teams as customers; has an internal product roadmap
- Goal: Self-service capability with compelling documentation

**3. Enabling Team**
- Helps stream-aligned teams acquire capabilities they lack
- Temporary by design: Enables, then steps back
- Examples: Security champions, observability specialists, mobile capabilities
- Goal: Skills transfer, NOT long-term dependency

**4. Complicated Subsystem Team**
- Owns components that require deep specialist knowledge
- Examples: ML model serving, video encoding, financial calculations
- Other teams use their output without needing to understand the internals

**Three interaction modes:**
- **Collaboration:** Two teams work together temporarily to discover new capabilities or solve a new domain
- **X-as-a-Service:** Platform team provides service; stream-aligned team consumes it
- **Facilitating:** Enabling team coaches; stream-aligned team does the work

### Dunbar's Number and Team Sizing

Robin Dunbar's research on human cognitive limits:
- **5:** Intimate trust (co-founders, senior leadership team)
- **15:** Genuine trust (your direct team, immediate colleagues)
- **50:** People you know well (department)
- **150:** Full organization where everyone knows everyone + shared culture (the "Dunbar limit")
- **500:** Acquaintances (recognizes faces, department)

**Engineering team implications:**
- Keep delivery teams at 5–9 people (product + engineering + design for stream-aligned)
- At 150 engineers, culture becomes management-dependent; explicit values documentation required
- At 50 people, add explicit coordination mechanisms (architecture review boards, tech leads)

### Scaling Phases

**0→10 people:** Founders do everything. No process. Speed is the strategy. Everyone knows everything. Hire generalists.

**10→50 people:** First functional roles (first dedicated PM, first dedicated designer, QA, DevOps). Explicit onboarding needed. Document what you do before anyone leaves. Technical debt begins accumulating.

**50→200 people:** Multiple product teams. Team Topologies becomes critical. OKRs replace everyone-in-the-same-room. Conway's Law kicks in hard. Hire specialists. First engineering director. Technical debt must be actively managed. Hiring bar discipline critical.

**200+ people:** Full HR/L&D. Director/VP level management needed. Architecture governance. Platform and stream-aligned team structure required. Cultural transmission becomes deliberate.

---

## Hiring

### Interview Process Design

**Signal density principle:** Each interview round should generate high-density signal on specific dimensions. Avoid repetitive rounds.

**Recommended structure for engineers:**
1. **Recruiter screen (30 min):** Motivation, timeline, compensation alignment
2. **Technical phone screen (45 min):** Problem-solving signal, communication, LLM/system design breadth
3. **Technical deep dive (60 min):** Coding, system design, or domain expertise
4. **Bar raiser / values interview (45 min):** Culture add, collaboration, growth mindset
5. **Hiring manager (30 min):** Team fit, role expectations, career path discussion

**Structured interviews:** Same questions, same rubric across all candidates. Reduces bias. Evidence that structured interviews have 2× higher predictive validity vs. unstructured.

**Work sample tests:** For senior roles, a take-home assignment (≤4 hours) or live pairing session provides the highest-signal evidence of actual work quality. Pay for work samples for contractors.

**Anti-patterns:**
- "Culture fit" interviews without defined criteria (proxies for similarity bias)
- Trivia questions (memorization ≠ engineering judgment)
- Whiteboard algorithms for roles that don't require them (SRE, data engineering, product engineering)

### Compensation Philosophy

**Levels and market data:** Use Levels.fyi, Radford/Mercer, Carta Total Comp, or Pave for benchmark data. Typical approach: pay at 50th–75th percentile base + competitive equity.

**Equity refreshes:** Top performers expect equity refreshes every 1–2 years. Without refreshes, equity cliff creates departure risk at year 4. Annual refresh grants at 25–50% of initial grant are market standard at Series B+.

**Compensation bands:** Define min/mid/max for each level. Hiring within band. Promotion moves to new band. This creates fairness and enables honest conversation.

**Geographic adjustment:** Many companies pay location-adjusted salaries. San Francisco = 1.0× multiplier; Austin = 0.85×; New York = 0.90×; Remote (low cost) = 0.75×. Controversial; different approaches have different retention implications.

---

## Technical Culture

### Psychological Safety (Amy Edmondson, Google Project Aristotle)

Psychological safety—belief that you won't be punished for speaking up—is the #1 predictor of high-performing teams (Project Aristotle, Google, 2012–2016).

**How to build it:**
- Leaders model vulnerability: share mistakes, ask for help publicly
- Reward speaking up: Thank people explicitly for raising concerns, especially when they turn out to be correct
- Decouple ideas from identity: "That idea needs refinement" not "That's a bad idea"
- Respond to failures with curiosity not blame: "What can we learn?" not "Who caused this?"
- No HiPPO (Highest Paid Person's Opinion) effect: Explicitly invite disagreement

**Four stages of psychological safety (Timothy Clark):** Inclusion safety → Learner safety → Contributor safety → Challenger safety. Must build in order.

### Blameless Postmortems

**Framework for production incidents:**
1. **Establish timeline:** Objective sequence of events (what happened, not who did what wrong)
2. **Contributing factors:** 5 Whys analysis on systemic causes
3. **Impact quantification:** Affected users, revenue, SLA breach, duration
4. **Action items:** Specific, assigned, time-bound prevention measures (not "be more careful")
5. **Distinguish:** Between causes (systemic) and triggers (proximate)

**"Just Culture" principle:** Individuals are rarely the root cause; systems and processes create conditions for failure. Punishing individuals discourages error reporting and leaves root causes unaddressed.

**Postmortem template:**
```
Incident: [Title]
Date/Duration: [start] to [resolution] (Xh Ym)
Severity: P1/P2/P3
Impact: [users affected, revenue impact, SLA breach]

Timeline:
  [timestamp] - [what happened]
  [timestamp] - [alert triggered]
  [timestamp] - [first responder engaged]
  [timestamp] - [mitigation applied]
  [timestamp] - [resolved]

Root Causes:
  1. [systemic cause]
  2. [contributing factor]

Action Items:
  - [ ] [specific change] | Owner: [person] | Due: [date] | Prevents: [failure mode]
  
Detection: How was this incident detected? Could we detect sooner?
Response: Was the runbook effective? What slowed resolution?
```

### Code Review Culture

**Author responsibilities:**
- Small PRs (<400 lines of meaningful code changes) — reviewers skip large PRs
- Descriptive PR descriptions: what changed, why, how to test
- Self-review before requesting review
- Mark draft PRs `[WIP]` or `[Draft]` explicitly
- Tag specific reviewers for domain expertise

**Reviewer responsibilities:**
- Review within 24 business hours (SLA for review turnaround)
- Ask questions before making demands; assume good intent
- Distinguish: blocking issues (must fix) vs. suggestions (nits, optional)
- Approve despite non-blocking nits ("LGTM with nits") — don't hold up ship for cosmetics
- Never: "Make it like I would have written it" (stylistic, no objective improvement)

**Code review checklist:**
- Correctness: Does it do what it claims?
- Tests: Are edge cases covered?
- Security: SQL injection, XSS, auth checks?
- Performance: N+1 queries, missing indexes, memory leaks?
- Error handling: What happens when external calls fail?
- Documentation: Are non-obvious choices explained?

### On-Call SLI/SLO/SLA Framework

**SLI (Service Level Indicator):** A specific quantitative measure of service behavior.
- Examples: Request latency (P50, P95, P99), error rate %, availability %, throughput

**SLO (Service Level Objective):** Target value for an SLI.
- Examples: "99.9% of requests will complete within 500ms over a 30-day rolling window"
- "Error rate will be < 0.1% of requests"

**SLA (Service Level Agreement):** Contractual commitment to customers; consequences for breach.
- SLA ≤ SLO (SLA is what you promise; SLO is what you aim for internally)

**Error budgets:** If SLO = 99.9% uptime, error budget = 0.1% = 43.8 minutes/month.
- If error budget is >50% consumed: Slow down feature releases, focus on reliability
- If error budget is <50% consumed: Can accept more risk, faster releases
- Resets monthly

**Alert design (symptom-based over cause-based):**
```
# Good: Alert on customer-impacting symptoms
alert_on: error_rate > 1%  # Customer sees errors
alert_on: p99_latency > 2s  # Customer experiences slowness

# Bad: Alert on every possible cause
alert_on: CPU > 80%  # Doesn't mean customer is impacted
alert_on: memory > 70%  # May not matter
```

---

## Agile Methodologies

### Scrum

**Roles:** Scrum Master (process facilitator, impediment remover), Product Owner (backlog priority, stakeholder interface), Development Team (self-organizing, 3–9 people).

**Events:**
- **Sprint Planning (2–4 hrs):** What will we build this sprint? Create sprint goal + sprint backlog
- **Daily Standup (15 min):** What did I do yesterday? What will I do today? Any blockers?
- **Sprint Review (1–2 hrs):** Demo completed work to stakeholders; get feedback
- **Sprint Retrospective (1.5 hrs):** What went well? What to improve? Action items for next sprint
- **Backlog Refinement (~2 hrs/week):** Estimate and clarify upcoming stories

**Velocity:** Average story points completed per sprint. Not a productivity measure—a capacity planning tool. Don't compare velocity across teams.

**When Scrum works:** Stable team, well-defined product domain, stakeholders available for frequent feedback, work can be decomposed into 1–5 day tasks.

**When Scrum fails:** Constantly shifting team composition, heavy dependency on outside teams, work can't be meaningfully demoed every 2 weeks, excessive ceremony over value.

### Kanban

**Principles:** Visualize workflow, limit WIP (Work In Progress), manage flow, make process policies explicit, implement feedback loops.

**WIP limits:** Each column in the Kanban board has a maximum number of items allowed. When a column is "full," team must resolve blocked items before pulling new work. Forces systemic improvement.

**Lead time vs. cycle time:**
- Cycle time: From work started → work delivered (process efficiency)
- Lead time: From work requested → work delivered (customer experience)

**When Kanban works:** Support/maintenance work, continuous flow without discrete sprints, variable-size work items, teams that don't need sprint-based planning.

### Shape Up (Basecamp)

6-week cycles. Design and engineering work together in "shaped" projects. No Sprints, no Scrum.

**Shaping:** Narrow down the problem, define the solution at the right level of abstraction (not wireframes, not vague either). Define "appetite" (how much time is this worth?).

**Betting table:** Leadership selects which shaped pitches to bet on each cycle. No carryover—if not done, it's a new bet next cycle.

**Hill charts:** Show work at "uphill" (figuring out what to do) vs. "downhill" (just executing) stages. No Gantt charts; no status meetings.

**When Shape Up works:** Product teams with strong designers, work that can be meaningfully completed in 6 weeks, cultures that trust teams to self-organize.

---

## Remote and Distributed Teams

### Async-First Communication

**Written communication as a multiplier:** Good async writing reduces meeting load, creates searchable documentation, and works across time zones.

**Communication defaults:**
- Default: async written (Slack, Linear, Notion, GitHub)
- When to move sync: Emotionally charged topics, complex back-and-forth (>5 messages), brainstorming, relationship building
- Never: Status updates in meetings (use async tools)

**Meeting hygiene:**
- Every meeting needs an agenda shared 24 hrs in advance
- Define: Decision needed, information shared, or collaboration required?
- Track action items in writing immediately after
- Aim to reduce meeting load by 50% through better async documentation

### Remote 1:1 Adaptations

- Video-on by default for 1:1s (relationship-critical)
- Virtual coffee chats: 20 min non-work catch-up sessions monthly
- Pay particular attention to isolation signals in monthly check-ins
- Annual in-person team gathering (critical for distributed teams—budget for it)

### Remote Hiring Considerations

- Assess async communication quality: How do candidates write? How clear are their work samples?
- Time zone overlap: Define minimum overlap hours required (4 hours is typical minimum)
- Home office stipend: $1,000–$3,000 setup + $50–$100/month for internet/equipment (competitive)
- Hire "no meetings" hours protectors: People who create async artifacts rather than calling meetings

---

## CTO vs. VP Engineering

Two different roles that are often confused:

| Dimension | CTO | VP Engineering |
|-----------|-----|----------------|
| Orientation | External-facing, innovation | Internal-facing, execution |
| Primary focus | Technology strategy, product roadmap, architecture | Delivery velocity, team health, processes |
| Main audience | Board, investors, customers, media | Engineering team, product managers |
| Time horizon | 2–5 years | Quarterly / annually |
| Key question | "What should we build?" | "How do we build it well?" |
| Risk tolerance | High (exploratory) | Low (predictable delivery) |

**Which to hire first:** Early-stage startups need a CTO (technical vision + first engineering hires). As the team grows past 20–30 engineers, the CTO often needs a VP Eng to handle operational management while the CTO focuses on strategy and architecture. Many early CTOs evolve into VP Eng roles (execution-oriented); others stay CTO-track (technical strategy).

---

## Build vs. Buy Decision Framework

**Build when:**
- Core differentiator of your product
- Unique requirements that off-the-shelf can't meet
- Team has deep expertise in the domain
- Cost of building < cost of buying over 3-year horizon
- IP value of the solution is high

**Buy when:**
- Commodity infrastructure (monitoring, authentication, payments, email)
- Time-to-market matters more than customization
- Maintenance burden of building is high
- Vendor has network effects that create switching barriers for competitors too

**Rule of thumb:** Never build what you can buy for commodity capabilities. Always build what creates competitive differentiation.

---

## AI Team Management

### Unique Challenges of AI Teams

**Probabilistic outputs:** AI systems produce non-deterministic results. Traditional QA (pass/fail) needs to be replaced with evaluation frameworks (scoring distributions, human evaluation, benchmark tracking).

**Experimentation culture:** ML research requires running many experiments that fail. Create psychological safety for "failed experiments" as learning, not performance failures.

**Evaluation-driven development:** Define evaluation metrics BEFORE building. Good evals are the foundation of reliable AI product development:
1. Define success criteria with stakeholders
2. Build a golden dataset of inputs with expected outputs
3. Track eval scores across model versions, prompt changes, fine-tuning iterations
4. Automated evals run on every change (CI for AI quality)

**Infrastructure complexity:** GPU cluster management, model serving, data pipeline engineering are specialized skills often separate from application engineering. Hire accordingly.

### AI Team Topologies

**Small AI startup (<20 engineers):**
- Generalist engineers who can work across AI infra + application
- One ML engineer who owns model quality and evaluation
- Avoid premature specialization

**Medium team (20–100 engineers):**
- AI/ML team: Model training, evaluation, fine-tuning
- Platform/ML Infra team: Model serving, feature store, data pipelines
- Product engineering team: Application layer, UX
- Data team: Analytics, training data quality, data pipelines

**Large team (100+ engineers):**
- Separate foundation model research vs. applied ML
- Platform team: ML serving, GPU infrastructure
- Product teams aligned to business domains
- Data science as a separate function with embedded partners

### Velocity Metrics for AI Teams

Traditional story points don't capture ML work well. Consider:
- Experiments run per sprint
- Model evaluation score improvements week-over-week
- Time to production for new model versions (evals → production pipeline time)
- Data labeling throughput (if using human annotation)
- Eval-to-production cycle time

---

## Quick Reference

**Hiring** → Structured interviews + work samples > unstructured conversation

**Performance reviews** → SBI feedback model, calibrated across managers, no surprises

**Team design** → Stream-aligned (most), Platform, Enabling, Complicated Subsystem (Team Topologies)

**OKRs** → Qualitative objective + 3–5 measurable KRs, 0.7 = success, don't tie to comp

**Postmortems** → Blameless, systemic root causes, 5 Whys, time-bound action items

**On-call** → SLI → SLO → SLA → error budgets → symptom-based alerts

**Scaling** → Dunbar 150 limit, Conway's Law, Inverse Conway Maneuver at each scale inflection

**Remote** → Async-first, written communication as a multiplier, annual in-person retreat
