# Tech Company Operating Models

## Go-To-Market Motion: PLG vs. Sales-Led vs. Community-Led

### Product-Led Growth (PLG)
PLG is a go-to-market strategy where product usage itself drives acquisition, retention, and expansion. The product is the primary sales and marketing channel. First coined in 2016 by Blake Bartlett at OpenView Partners. Core mechanics:

- **Free tier / freemium** removes friction at top of funnel
- **Time-to-value** is the critical metric — how fast does a new user reach the "aha moment"?
- **Product Qualified Leads (PQLs)** replace Marketing Qualified Leads (MQLs) as the sales handoff signal — PQLs are users who have hit activation thresholds (e.g., 3 projects created, 10 team invites sent, 5 API calls made)
- **Expansion revenue** comes from usage-based pricing or seat expansion within accounts that self-serve in

**When PLG works**: Developer tools, productivity software, bottoms-up B2B where the end user is also the buyer (or can influence the buyer). Benchmark: Slack, Figma, Notion, Stripe, Twilio, Datadog in early stages.

**PLG metrics that matter**:
- Activation rate (% of signups reaching the "aha moment" within 7 days)
- Time-to-value (median hours from signup to first meaningful action)
- PQL conversion rate (% of PQLs that convert to paid)
- Net Revenue Retention (NRR) — must be >120% for a healthy PLG engine
- Viral coefficient (invites sent per activated user)

**Anti-pattern**: Companies declare PLG but then starve product investment, neglect onboarding, and run a pure outbound sales motion on top. The result is neither PLG nor sales-led — it's just expensive.

### Sales-Led Growth (SLG)
SLG relies on outbound prospecting, AEs, and relationship-based selling. Appropriate when:
- Deal sizes are large (>$50K ACV) and the buying process is complex
- The product requires significant implementation or customization
- The buyer is an executive or committee, not an end user
- Security, compliance, or legal approvals are required

**When to transition from PLG to SLG**: When product-led signups start converting to enterprise contracts. The "PLG → enterprise" transition requires building: a dedicated enterprise sales team, a solution engineering function, a customer success motion, and enterprise features (SSO, audit logs, SLAs, custom contracts).

**The hybrid model (2025 standard)**: Most mature PLG companies now run product-led + sales-assisted. Self-serve for SMB/mid-market, enterprise sales overlay for large accounts. The key insight: sales reps in a hybrid model are "expansion hunters" — they work existing product-led accounts, not cold prospects.

### Community-Led Growth
Community-led growth uses a community of users/advocates as the primary growth flywheel. The mechanism: value creation → community participation → advocacy → new user acquisition. Best examples:

- **HashiCorp**: Nearly all paid adoption begins with open-source usage. Developers discover Terraform/Vault through community, adopt free, upgrade to enterprise.
- **Stripe**: Obsessive developer experience, API-first design, and developer relations make the product its own community channel.
- **Twilio**: Developer community + simple API integration + generous free tier created a self-sustaining developer ecosystem.

**Community-led flywheel**:
1. Build a product developers or power users love
2. Invest in documentation, tutorials, and developer experience to near-perfection
3. Create community infrastructure (Discord, forums, GitHub Discussions)
4. Recognize and amplify community champions
5. Open-source key components to increase surface area
6. Layer enterprise features and sales on top of proven open-source adoption

**The critical mistake**: Companies create community as a marketing tactic (a Slack group for announcements) rather than as a genuine value exchange.

---

## Team Structure Frameworks

### Amazon Two-Pizza Teams
Jeff Bezos's rule: if you can't feed a team with two pizzas, it's too big. Optimal team size: 6-8 people. The principle behind this: cognitive overhead of coordination grows exponentially with team size; beyond ~8, people stop having shared context on the full problem.

**Implementation rules**:
- Each team owns a service or capability end-to-end (build it, run it)
- Teams must be loosely coupled and independently deployable
- Team owns its own metrics and owns its roadmap within broader company strategy
- Communication between teams happens through APIs, not meetings

**Warning**: Two-pizza teams without clear ownership boundaries and clear API contracts create chaos. The Amazon approach only works because of "working backwards" (press release first, then build), strong internal service APIs, and explicit ownership of on-call responsibilities.

### Team Topologies (Matthew Skelton & Manuel Pais)

The definitive modern framework for organizing engineering teams. Four team types:

**1. Stream-Aligned Teams** (majority of teams — 60-70%)
- Aligned to a flow of customer or business value (a product, a user journey, a domain)
- Own a slice of the product end-to-end: frontend, backend, data, deployment, on-call
- Primary cognitive load: the business domain they serve
- Anti-pattern: stream-aligned teams that are constantly blocked waiting for platform or shared services teams

**2. Platform Teams** (15-20%)
- Build and maintain internal developer platforms (IDPs) — CI/CD, observability, cloud infrastructure, security tooling, data pipelines
- Goal: reduce cognitive load on stream-aligned teams via self-service, not via tickets
- The platform is a product; the stream-aligned teams are its customers
- Success metric: how independently can stream-aligned teams deploy? If they're filing tickets to the platform team for every deployment, the platform has failed
- Anti-pattern: platform teams that become gatekeepers; stream-aligned teams should be able to move without asking permission

**3. Enabling Teams** (temporary, 5-10%)
- Specialist coaches who help stream-aligned teams adopt new practices (security, accessibility, machine learning, architecture patterns)
- Time-limited engagements — they upskill and move on, they do not become permanent dependencies
- Anti-pattern: enabling teams that linger and become shadow platform teams

**4. Complicated-Subsystem Teams** (rare)
- Own genuinely complex technical domains where deep specialist knowledge is required (video encoding, physics engines, recommendation algorithms)
- Reduce cognitive load on stream-aligned teams by presenting a stable API to the complexity underneath

**Three Interaction Modes**:
- **Collaboration**: Two teams working closely together to solve a problem. Appropriate for short periods when building new capabilities. High cognitive load — cannot be sustained long-term.
- **X-as-a-Service**: Platform team provides capability via self-service API or tool. Stream-aligned team consumes with minimal interaction. The long-term steady state.
- **Facilitating**: Enabling team coaches another team through a change. Time-boxed. Goal is team autonomy.

**Cognitive Load as the Primary Constraint**: The fundamental insight of Team Topologies is that you cannot add responsibility to a team without removing something else. Every team has a cognitive load budget. When it overflows, quality drops, velocity drops, and engineers burn out. Organizational design is fundamentally about managing cognitive load distribution.

### Spotify Model
Squads (autonomous teams), Tribes (collection of related squads), Chapters (functional discipline groups across squads), Guilds (communities of interest). Originally published in 2012, widely copied, often misunderstood.

**What actually works from the Spotify model**:
- Squad autonomy: small teams with full ownership of a product area
- Chapter model for engineering excellence: engineers in similar disciplines (frontend, backend, data) share practices, standards, and career development across squads
- Guild model for cross-cutting interests: optional communities of practice (security, machine learning, open source)

**What Spotify itself abandoned**: By 2020, Spotify had quietly moved away from the original model. The creators (Henrik Kniberg and Anders Ivarsson) had left the company. Many adopters struggled with the "tribe" layer adding bureaucracy without value, misaligned incentives between chapter leads and squad leads, and the reality that Spotify's culture was not transferable through an org chart.

**2025 reality**: Successful companies run an "adapted Spotify" — squads + chapters + optional guilds — combined with Team Topologies team types and interaction modes, OKR alignment, and platform engineering principles. The framework itself matters less than the principles: team autonomy, clear ownership, cognitive load management, and fast feedback loops.

---

## Delivery Frameworks: Scrum vs. Kanban vs. Shape Up

### Scrum
Fixed-length sprints (1-2 weeks), sprint planning, daily standups, sprint review, retrospective. Appropriate for:
- Teams that need to coordinate across multiple squads
- When forecasting and predictability matter to stakeholders
- When the team is learning how to work together and needs structured ceremonies

**Scrum anti-patterns**: Using sprint velocity as a performance metric (it incentivizes sandbagging). Treating backlog grooming as just story estimation. Daily standups that are status reports to the manager rather than team coordination.

### Kanban
Continuous flow, WIP limits, pull-based. Appropriate for:
- Operations, support, and maintenance work (unpredictable, continuous flow)
- Teams handling a mix of planned and unplanned work
- Mature teams that have outgrown the scaffolding of sprints

**Key Kanban metrics**: Lead time (request to delivery), cycle time (start to delivery), throughput (items delivered per week), WIP age (how old are open items?). Flow efficiency = active time / total time; industry average is 15-25%, meaning 75-85% of time is waiting.

### Shape Up (Basecamp)
Six-week build cycles with 2-week cooldowns. Work is "shaped" before the cycle starts — a pitch defines the problem, the rough solution, and the appetite (how much time is worth spending). No sprints, no backlog, no daily standups, no velocity.

**Key principles**:
- **Appetite over estimates**: "How much time is this worth?" replaces "How long will this take?" A fixed time budget forces scope decisions upfront.
- **Shaping**: Senior designers and engineers shape work before it's assigned — they define the problem, propose a rough solution, and identify the rabbit holes to avoid. Shaping happens off-cycle.
- **Betting Table**: Leadership bets on shaped pitches for each cycle. Unbet work doesn't go in a backlog — it might come back next cycle if still relevant.
- **Hill Charts**: Teams self-report progress by placing work on a hill (uphill = figuring it out, downhill = executing). Reveals when teams are stuck before the cycle ends.
- **No carry-over**: Unfinished work at cycle end is cut, not carried over. Forces honest scoping. Forces shaping quality.

**When Shape Up wins over Scrum**:
- Product companies with senior, autonomous builders
- Work that naturally takes 4-8 weeks to be meaningful (forces you to think end-to-end)
- When sprint ceremonies feel like overhead rather than value
- When you want to eliminate the backlog as a psychological burden

**Shape Up failure modes**: Insufficient shaping quality (teams discover rabbit holes mid-cycle), team members not senior enough to exercise the autonomy the model requires, organizations that need cross-team sprint coordination.

### OKRs: Google Style vs. Intel Style

**Intel (Andy Grove) original**: All OKRs were aspirational. Setting the bar so high that teams fail to fully achieve them but accomplish more by trying. Grade on a 0-1 scale; 0.7 was considered excellent.

**Google's evolution**: Separated committed OKRs (must achieve 1.0) from aspirational OKRs (designed to reach ~0.7). This distinction prevents the system from being used to hide sandbagging while preserving moonshot thinking.

**CFRs (Conversations, Feedback, Recognition)**: John Doerr's companion to OKRs. OKRs without continuous dialogue become static documents. CFRs replace annual performance reviews with ongoing check-ins, real-time feedback, and explicit recognition.

**OKR implementation playbook**:
1. Company-level OKRs set by leadership (3-5 objectives, 2-4 KRs each)
2. Team-level OKRs aligned to company OKRs but not copied from them — teams should own "how" they contribute
3. Quarterly cadence with weekly check-ins
4. Grade at quarter end: 0.7+ on aspirational OKRs is success; <1.0 on committed OKRs is a problem
5. Never attach OKR grades to compensation — it destroys aspirational goal-setting
6. Limit to 3-5 objectives per team; more dilutes focus

**OKR failure modes**: Too many OKRs (laundry lists not priorities), treating OKRs as performance management tool (incentivizes sandbagging), KRs that are outputs not outcomes ("launch feature X" vs. "increase activation rate by 15%"), missing the rhythm (set and forget, no weekly check-ins).

**OKR vs. Hoshin Kanri**: Hoshin Kanri is a complete strategic planning methodology (Japanese, Toyota origin) while OKRs are primarily a goal-setting framework. Hoshin Kanri's "catchball" — iterative up-down-up communication to align strategy — is more rigorous than top-down OKR cascade. At scale (500+ people), combining Hoshin's deployment methodology with OKRs' quarterly agility is powerful. At early stage, OKRs alone suffice.
