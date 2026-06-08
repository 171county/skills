# People & Culture for Tech Companies

## Remote-First Culture: The GitLab Handbook Model

GitLab is the world's largest fully remote company (~2,100 people across 60+ countries). Their model is the gold standard for distributed-first culture. Core principles:

### The Handbook-First Principle
Document everything in the company handbook *before* communicating it anywhere else. The hierarchy:
1. Write it in the handbook
2. Link to the handbook when communicating via Slack, email, or meeting
3. Never repeat important information in ephemeral channels only — it disappears

**Why this works**: A 2,700+ page public handbook means any new team member can self-onboard. Any decision that was made is documented. Any policy question has an authoritative answer. The handbook is the source of truth, not the manager's memory or a meeting recording.

**The handbook as a forcing function**: If a process isn't documented, it doesn't exist in a reliable sense. This forces operational rigor in a way that office culture hides.

### Remote-First vs. Remote-Friendly
- **Remote-friendly**: Office is default; remote is accommodated. Async is second-class.
- **Remote-first**: Remote is the default; office (if any) is just another location. Every meeting, process, and communication artifact is designed for distributed participation.

The failure mode: "remote-friendly" where executives are co-located, important decisions are made in hallway conversations, and remote workers are permanently disadvantaged.

---

## Async Communication: Principles and Implementation

### The Async Default
Default to async. Use synchronous (real-time) communication only when the problem genuinely requires it:
- **Use async for**: Project updates, decisions that don't need immediate input, documentation, reviews, brainstorming
- **Use sync for**: Relationship building, complex problems with high uncertainty, conflict resolution, onboarding

### Async-First Communication Stack
- **Long-form writing**: Notion, Confluence, or a wiki for decisions, proposals, retrospectives
- **Short-form messages**: Slack/Teams with explicit norms (no expectation of immediate response; use @channel sparingly)
- **Video**: Loom for demos, walkthroughs, and messages where tone matters
- **Issue trackers**: GitHub Issues, Linear, Jira for all actionable work items

### Async Meeting Norms
Eliminate: recurring status update meetings, meetings without a written agenda, meetings where one person talks at everyone else.
Replace with: written status updates, shared documents with commenting, recorded walkthroughs.

Use synchronous meetings for: alignment on complex decisions (after async pre-reading), 1:1s, retrospectives, team building.

### Documentation Culture
**The documentation hierarchy**:
1. **Reference documentation**: API docs, system architecture, runbooks — must be accurate, maintained, discoverable
2. **Process documentation**: How we do X — onboarding, deployment, incident response, performance reviews
3. **Decision documentation**: Why we made a particular architectural or strategic decision (Architecture Decision Records / ADRs)
4. **Knowledge base**: FAQs, internal guides, tribal knowledge made explicit

**Making documentation stick**: The bottleneck is not tooling, it's culture. Documentation culture requires: leaders who model it (they write things down), attribution so contributors get credit, and making it easier to write than to not write (templates, prompts, AI assistance).

---

## Psychological Safety and High-Performance Teams

### Google's Project Aristotle (2012-2014)
Google analyzed 180+ teams across 250+ attributes to identify what makes teams high-performing. The finding: **psychological safety was the single most important factor** — more than team composition, experience, or technical skill. Teams with high psychological safety had:
- 19% higher productivity
- 31% more innovation
- 27% lower turnover
- 3.6x higher engagement
- Psychological safety accounted for 43% of the variance in team performance

### The Five Dynamics of High-Performing Teams (Project Aristotle)
1. **Psychological safety** (most critical): Can I take risks on this team without being penalized for speaking up?
2. **Dependability**: Can I count on teammates to deliver on time and with quality?
3. **Structure and clarity**: Are goals, roles, and plans clear?
4. **Meaning**: Is the work personally meaningful to team members?
5. **Impact**: Do team members believe their work creates change?

### Building Psychological Safety
- Managers must **model vulnerability**: admit mistakes publicly, ask for help, say "I don't know"
- Create **explicit norms for disagreement**: "disagree and commit" only works when people genuinely can disagree before the commit
- **Blameless incident culture** is the engineering equivalent of psychological safety
- **Retrospectives** done well are psychological safety in practice — what went wrong and how do we fix the system?

### The Performance-Safety Balance
Psychological safety is necessary but not sufficient for high performance. Teams need both safety (freedom to take risks) AND high accountability (clear standards, honest feedback). The failure modes:
- High safety + low accountability = "comfort zone" (no growth, mediocre output)
- Low safety + high accountability = "anxiety zone" (performance through fear, not sustainable)
- High safety + high accountability = "learning zone" (optimal for tech teams)

---

## Compensation Benchmarking

### Data Sources by Use Case

**Levels.fyi**
- Best for: Real-time market data for software engineering, product management, and design roles at established tech companies
- Methodology: Self-reported, skews toward larger tech companies and higher-than-median compensation
- Use as: A floor check and negotiation reference; "directionally correct" per Y Combinator
- Limitation: Sparse data for roles below Staff/Senior at smaller companies

**Radford (Aon)**
- Best for: Enterprise compensation planning, systematic benchmarking across all functions
- Methodology: Institutional survey (subscription required), more statistically robust
- Use for: Setting formal pay bands, equity ranges, and bonus targets at Series B+

**Mercer**
- Similar to Radford; more global coverage; preferred for multi-geography compensation benchmarking

**Carta / Pave / Carta Total Comp**
- Best for: Startup and growth-stage compensation, especially equity benchmarking
- Real-time private company data; strong for understanding market equity grant sizes at each stage

### Compensation Philosophy
Define your philosophy explicitly before setting pay:
1. **Target percentile**: Which market percentile do you target for base? (50th, 65th, 75th?) Higher targets cost more; lower targets require equity premium.
2. **Geographic differentiation**: Single global rate (GitLab's approach, adjusted by local cost-of-market) vs. location-based bands (Google, Meta approach)?
3. **Equity philosophy**: How much of total comp comes from equity? At what stages/levels?
4. **Pay transparency**: Internal transparency builds trust; external transparency reduces negotiation friction but reveals internal compression issues

### The Compression Problem
Market rates rise faster than internal salary bands at most companies. Engineers hired 3 years ago may be paid below what new hires receive for the same role. Unaddressed compression causes attrition of your most experienced people. Solutions:
- Annual market adjustments (separate from performance-based increases)
- Off-cycle equity refreshes targeted at at-risk employees with below-market total comp
- Transparent band publishing so engineers can see the range and self-advocate

---

## Equity Design: Refreshes and Vesting

### Initial Grants
Standard vesting: 4 years with a 1-year cliff. The cliff protects the company from early departures; 4-year vesting aligns employee and company timelines.

### Refresh Grants
Refresh grants are additional equity grants given to retain employees after initial grants approach full vesting. 2025 best practices:
- **When to refresh**: When an employee is 18-24 months from full vest on their initial grant ("vest cliff")
- **Vesting trend shift**: 20% of companies in 2025 are shifting from time-based to hybrid performance/time-based vesting for refresh grants (Sequoia data)
- **Size compression**: Refresh grant sizes have shrunk significantly. 2023: 48% of executive refresh grants were >50% of original hire grant size. 2024: only 6% reached that level. The days of giant recurring refreshes are over at most companies.

### Performance-Based Vesting
Increasingly common for refreshes at senior levels. Typically structured as:
- Time-based component (e.g., 75% vests on schedule)
- Performance modifier (e.g., 0.5x-1.5x multiplier based on OKR achievement or specific metrics)

**Warning**: Performance-based equity requires rigorous, unambiguous measurement criteria. If the metrics are subjective or controlled by the manager, you've created a retention risk tool that penalizes high performers for having a bad manager.

### Equity Refresh Program Design
1. Set a refresh eligibility window (typically 18-24 months after initial grant, then annually)
2. Establish a refresh pool (typically 1-2% of fully diluted shares annually for the whole company)
3. Calibrate refresh sizes by level — senior levels receive larger refreshes in absolute terms
4. Tie refreshes to market data and performance — not to tenure alone
5. Communicate the refresh program explicitly during onboarding so employees understand how it works

---

## Hiring for Engineering Teams

### The Interview Framework
- **Behavioral interviews**: Past behavior predicts future behavior better than hypotheticals. Use STAR format (Situation, Task, Action, Result). Ask for specifics, not narratives.
- **Technical assessment**: Relevant to actual work — not algorithm puzzles if the role is building CRUD APIs. Calibrate difficulty to the level you're hiring.
- **System design interviews** (for mid-senior+): Assess architecture thinking, tradeoff articulation, and communication — not memorized patterns.
- **Calibration panel**: Multiple independent evaluators reduces bias. Compare scores before group discussion (anchoring bias is real).

### Structured Hiring Process
1. Intake: Define the role requirements, key competencies, and must-have vs. nice-to-have before sourcing
2. Consistent interview structure across all candidates for the same role
3. Scorecard-based evaluation (not holistic "would I work with them?")
4. Diverse interview panels reduce demographic bias
5. Explainable hiring decisions — could you defend your decision in writing?

### Engineering Career Development
- **Individual Development Plans (IDPs)**: Each engineer has a documented IDP with 90-day, 6-month, and 12-month milestones. Managers review and update quarterly in 1:1s.
- **Mentorship vs. sponsorship**: Mentors give advice; sponsors advocate for you. Senior engineers who sponsor junior talent are more valuable to the org than those who mentor.
- **Stretch assignments**: The highest-ROI development activity. Assign engineers to projects that require demonstrating the next level's capabilities.
