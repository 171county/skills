# Program & Project Management

## Project vs. Program vs. Portfolio

- **Project**: A time-boxed effort to produce a specific deliverable. One team, one outcome.
- **Program**: A collection of related projects managed together to achieve strategic outcomes not possible by managing projects individually. Cross-team, multi-quarter.
- **Portfolio**: All programs and projects across an organization, managed for strategic alignment and resource optimization.

Most tech companies under-invest in program management. The symptom: initiatives that take 3x longer than expected, repeated discovery of cross-team dependencies in the last week before launch, and executives surprised by slippage.

---

## Milestone-Based vs. Continuous Delivery

### Continuous Delivery (CD)
The default for most product engineering teams. Features are developed, tested, and deployed independently on a continuous flow. No artificial "launch event" — value ships when it's ready.

**When CD is correct**:
- Web/mobile products where deployment is risk-manageable
- Teams with high test coverage and deployment automation (DORA: elite or high)
- Feature work that doesn't require cross-system coordination

**CD with feature flags**: Use feature flags to decouple deployment from release. Code ships to production but is gated behind a flag. Release is a business decision, not a technical one. This enables: gradual rollouts, A/B testing, kill switches, and separating deployment risk from release risk.

### Milestone-Based Delivery
Appropriate for:
- Major platform changes that require cross-team coordination (database migrations, API versioning, authentication changes)
- Regulatory or compliance deliverables with hard deadlines
- External commitments (contractual launch dates, conference announcements)
- Hardware-dependent software where deployment windows are constrained

**Milestone design principles**:
- Milestones should represent a state of verified readiness, not just a date
- Define acceptance criteria for each milestone upfront — not "done when it's done"
- Build buffer into milestone schedules (20-30% for uncertainty; more for novel work)
- Distinguish "the work is done" from "the work is verified and ready to proceed"

---

## Critical Path Method (CPM)

**The critical path** is the longest sequence of dependent tasks that determines the earliest possible completion date for the project. Delay any task on the critical path, and the project end date slips by the same amount. Delay a task *off* the critical path (with float), and the project end date doesn't move.

### Implementation Steps
1. **List all tasks** with realistic duration estimates
2. **Identify dependencies**: Which tasks must complete before another can start? (Finish-to-Start is most common)
3. **Build the network diagram**: Map tasks and their dependencies
4. **Calculate forward pass** (earliest start and finish for each task)
5. **Calculate backward pass** (latest start and finish without delaying the project)
6. **Identify float** (slack): tasks where late finish > early finish. Zero-float tasks are on the critical path.

**Near-critical paths**: Tasks with very low float (1-3 days) are at risk of becoming critical. Monitor these closely — they're where surprises originate.

**In practice**: Most tech teams use a simplified version: identify the top 3-5 dependency chains, determine which one has the least float, and focus risk management there. Full CPM is warranted for large programs (>10 teams) or fixed-deadline projects.

---

## Dependencies Management

Dependencies are the #1 source of unexpected slippage in multi-team programs.

### Dependency Types
- **Finish-to-Start (FS)**: Team A must finish before Team B can start. Most common, most risky.
- **Start-to-Start (SS)**: Team B can start when Team A starts (can run in parallel after the initiating event)
- **Finish-to-Finish (FF)**: Teams must finish at the same time (e.g., frontend and backend API must both be done before integration can complete)
- **API/Interface dependencies**: Team A is building a capability that Team B's work depends on. Manage by defining the API contract first, then building in parallel (Team B mocks Team A's API).

### Dependency Register
Maintain a dependency register for any program involving 3+ teams:

| Dependency | Upstream Team | Downstream Team | Delivery Date | Status | Risk |
|------------|--------------|-----------------|---------------|--------|------|
| Auth API v2 | Platform | Checkout | Sprint 8 | On track | Low |
| Payment SDK | Payments | Mobile | Sprint 6 | At risk | High |

Review the dependency register weekly in a cross-team program sync. Escalate blockers immediately — waiting for the next sprint review is too slow.

### Interface-First Development
For inter-team dependencies: define the API/interface contract before implementation begins. This unblocks both teams to work in parallel:
- Upstream team builds to the spec
- Downstream team mocks the dependency and builds against the mock
- Integration happens late, but isn't on the critical path

---

## Risk Registers

### Risk vs. Issue
- **Risk**: Something that might happen. Has probability and impact.
- **Issue**: Something that has already happened. Needs immediate action.

Manage risks proactively; respond to issues reactively. Most program managers only track issues — by the time a risk becomes an issue, your mitigation options are limited.

### Risk Register Structure

| Risk ID | Description | Probability (H/M/L) | Impact (H/M/L) | Risk Score | Owner | Mitigation | Trigger |
|---------|-------------|--------------------|--------------------|------------|-------|------------|---------|
| R01 | 3rd party API v2 deprecation before integration complete | M | H | High | Team Lead | Begin migration sprint earlier; negotiate timeline with vendor | Vendor notice received |
| R02 | Key engineer attrition during critical path | L | H | Medium | EM | Document architecture decisions now; cross-train backup | Resignation received |

**Risk scoring**: Probability × Impact. Focus active mitigation on High × High risks; monitor Medium risks weekly; accept Low risks.

**Triggers**: Define what event converts a risk into an issue. Knowing the trigger in advance enables faster response.

---

## Stakeholder Communication

### The Communication Hierarchy
Different stakeholders need different information at different frequencies:

| Stakeholder | Frequency | Format | Content |
|-------------|-----------|--------|---------|
| Executive sponsor | Monthly | 1-page narrative | Status vs. plan, risks requiring escalation, decisions needed |
| Functional VPs | Weekly | Status dashboard + Slack summary | Milestone status, dependency blockers, headcount needs |
| Team leads | Daily | Standup or async written update | Task status, blockers, handoffs |
| Engineering teams | Real-time | Jira/Linear + Slack | Task assignments, PR reviews, deployment status |

**Executive communication principle**: Executives want three things — (1) are we on track? (2) what do I need to decide? (3) what do I need to worry about? Every executive update should answer these three questions in the first paragraph.

### The Status Update Format
Use RAG (Red/Amber/Green) status with honest assessment:
- **Green**: On track. No action needed from leadership.
- **Amber**: At risk. Mitigation plan is in place; may need escalation if plan fails.
- **Red**: Off track or blocked. Needs leadership action, decision, or resource allocation.

**Anti-pattern**: Status reports that are always green until the launch date when everything turns red simultaneously. This is a change management failure — bad news must travel faster than good news.

### Change Management for Programs
When scope, timeline, or resources change:
1. Identify the cause of the change
2. Assess the impact on timeline, dependencies, and quality
3. Generate options (accelerate using more resources / reduce scope / extend timeline)
4. Get explicit stakeholder sign-off on the chosen option
5. Update all downstream communication artifacts

---

## Program Management at Scale

### Program Charter
Every program needs a charter before work begins:
- **Objective**: What outcome are we producing and why does it matter?
- **Success criteria**: How will we know we succeeded? (Measurable outcomes)
- **Scope**: What is in scope? What is explicitly out of scope?
- **Timeline**: Key milestones and end date
- **Stakeholders**: Who is responsible, accountable, consulted, and informed? (RACI)
- **Resources**: Budget, headcount, and tooling required
- **Risks**: Top 3-5 risks and initial mitigations

### Cross-Team Program Rhythms
- **Weekly program sync** (30-45 min): Cross-team leads, dependency review, blocker escalation
- **Monthly program review** (60 min): Executive stakeholders, milestone status, scope/timeline decisions
- **Quarterly program retrospective**: What went well, what slowed us down, process improvements for next quarter

### The "Accountability Tax" Problem
Program management adds coordination overhead. The tax is worth paying when the program is genuinely cross-team. It is not worth paying for single-team initiatives — over-process is as dangerous as under-process. Apply the simplest coordination mechanism that reliably prevents problems.

**When to add structure**: When the same type of problem (missed dependency, late integration, scope creep) has happened twice in 6 months, add structure to prevent the third occurrence. Don't anticipate every possible problem — respond to the ones that actually occur.
