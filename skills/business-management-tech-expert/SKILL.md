---
name: business-management-tech-expert
description: Use this skill when you need expert-level knowledge about managing technology organizations, engineering teams, and tech-forward businesses. Triggers on questions about: Team Topologies (stream-aligned, platform, enabling, complicated subsystem teams), Conway's Law and inverse Conway maneuver, engineering leadership (VP Eng vs CTO roles, staff+ engineering career paths), DORA metrics (deployment frequency, lead time, MTTR, change failure rate), psychological safety and Project Aristotle, OKRs for engineering teams, scaling organizations (10→100→1000 employees), AI-augmented management, DevOps and platform engineering culture, build/buy/partner decisions, customer success team structure and health scoring, business development and partnership strategy, SOX compliance for tech companies, and organizational design principles.
---

# Business Management Tech Expert

## Organizational Design Principles

### Team Topologies Framework (Matthew Skelton & Manuel Pais)
The definitive framework for structuring technology organizations to optimize for flow.

**Four fundamental team types:**

**1. Stream-Aligned Team:**
- Aligned to a single valuable stream of work (product, service, user journey)
- Cross-functional: owns the full stack end-to-end (frontend, backend, data, QA, PM)
- Owns, operates, and evolves its service in production
- The dominant team type — everything else exists to support stream-aligned teams
- Anti-pattern: Teams organized by technical layer (frontend team, backend team)

**2. Platform Team:**
- Provides internal services that stream-aligned teams consume as self-service
- Goal: Reduce cognitive load on stream-aligned teams
- Builds: CI/CD pipelines, deployment infrastructure, observability tooling, dev environments
- Platform-as-a-product: Treats internal teams as customers, measures NPS, documents APIs
- Success metric: Stream-aligned teams are unblocked; they don't need to talk to platform team for normal work

**3. Enabling Team:**
- Temporary: Helps stream-aligned teams acquire capabilities they currently lack
- Does NOT own anything long-term — it transfers knowledge and moves on
- Examples: Security enablement, accessibility, ML/AI adoption teams
- Has an expiry: Once the capability is transferred, the enabling team dissolves or moves on
- Anti-pattern: Enabling teams that become permanent gatekeepers

**4. Complicated Subsystem Team:**
- Owns a technical subsystem requiring specialist expertise not available on stream-aligned teams
- Examples: DSP team, ML training infrastructure, custom rendering engine
- Delivers components as libraries or APIs to stream-aligned teams
- Use sparingly — complexity is its own organizational tax

### Conway's Law and the Inverse Conway Maneuver
> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations." — Mel Conway, 1967

**Practical implication:** Your software architecture mirrors your org chart. To change your architecture, change your org structure (Inverse Conway Maneuver).

**Inverse Conway Maneuver:**
1. Define the target architecture (microservices vs. monolith, domain boundaries)
2. Design teams to own each architectural component
3. Teams will naturally evolve the architecture to match their boundaries
4. Communication patterns drive API design

**Warning signs of Conway's Law problems:**
- Multiple teams must coordinate for every release
- Services have unclear ownership ("this touches 3 teams")
- Architecture reflects the current org chart, not the desired product structure

### Cognitive Load Management
Teams can only hold so much in their heads. When cognitive load exceeds capacity, quality, speed, and morale suffer.

**Three types of cognitive load (John Sweller):**
1. **Intrinsic**: Complexity inherent to the work (domain logic)
2. **Extraneous**: Complexity from how the work is presented (tooling, process)
3. **Germane**: Complexity that builds understanding and skill

**Management goal:** Minimize extraneous load, protect space for intrinsic and germane load.

**Reducing extraneous cognitive load:**
- Platform team abstracts away infrastructure decisions
- Clear boundaries reduce coordination overhead
- Good documentation reduces "who do I ask?" load
- Automated testing removes "will my change break something?" anxiety

---

## Engineering Leadership

### CTO vs. VP Engineering

| Dimension | CTO | VP Engineering |
|-----------|-----|----------------|
| Focus | External + future | Internal + present |
| Time horizon | 3-5 years | 6-18 months |
| Primary activities | Thought leadership, investor relations, recruiting brand, technology vision | Delivery, team health, process, hiring execution |
| Technical depth | Visionary, often less hands-on over time | Operationally technical, familiar with current stack |
| Relationship | Sets technical direction | Executes technical direction |

**Small startup (Seed/Series A):** Typically one person covers both roles (CTO-of-all-trades)
**Growing startup (Series B+):** Need to hire a strong VP Eng to allow CTO to focus externally

### Staff+ Engineering Career Framework
Senior → Staff → Principal → Distinguished → Fellow

**Staff Engineer archetypes (Will Larson):**
1. **Tech Lead**: Guides technical direction of a team or group
2. **Architect**: Owns cross-system technical decisions, design authority
3. **Solver**: Parachuted into hard problems, deep focused work
4. **Right Hand**: Amplifies a senior leader's scope

**Staff Engineer operating principles:**
- Work through others, not just yourself (leverage)
- Set technical strategy (where we're going + why)
- Find and create the biggest bottlenecks in engineering
- Be a force multiplier on the team, not an individual contributor

### Engineering Manager (EM) Effectiveness

**Span of control:**
- Junior EM: 4-6 direct reports (hands-on, still coding occasionally)
- Experienced EM: 6-8 direct reports
- Senior EM / Director: 8-12 (through leads)

**1:1 best practices:**
- Weekly, 30 minutes minimum
- Their agenda first (not status updates)
- Alternate between: career development, current blockers, feedback, work quality
- Long-term tracking: growth goals, compensation timeline, promotion criteria

**Project Aristotle (Google) — What makes teams effective:**
Google's five-year study of 180 teams found the #1 predictor of team effectiveness is:

1. **Psychological Safety** (most important): Can team members take risks without fear of punishment?
2. **Dependability**: Do members deliver what they promise?
3. **Structure and Clarity**: Are roles, plans, and goals clear?
4. **Meaning**: Does the work have personal significance?
5. **Impact**: Do members believe their work matters?

**Building psychological safety (Amy Edmondson):**
- Leaders go first: Admit mistakes publicly, show vulnerability
- Reframe failure as learning (blameless post-mortems)
- No punishment for bad news (kill the messenger = silence in future)
- Invite input explicitly: "What am I missing?" "What would you do differently?"
- Acknowledge when people raise hard issues: "I'm glad you said that"

---

## DORA Metrics and Engineering Productivity

### 2025 DORA Report — Seven Performance Archetypes

The 2025 DORA State of DevOps Report identified 7 distinct performance archetypes across organizations:

**Elite performers (~20% of orgs):**
- Deployment frequency: Multiple times per day
- Lead time for change: <1 hour
- Change failure rate: <5%
- MTTR: <1 hour

**High performers (~25%):**
- Deployment frequency: Daily to weekly
- Lead time: 1 day to 1 week
- Change failure rate: 5-10%
- MTTR: <1 day

**Medium performers (~30%):**
- Deployment frequency: Weekly to monthly
- Lead time: 1 week to 1 month
- MTTR: 1 day to 1 week

**Low performers and 4 additional archetypes (struggling in specific dimensions):**
- Frequent deployments but high failure rate (speed without quality)
- Slow deployments but very low failure rate (quality without speed)
- Strong incident response but poor deployment pipeline
- Platform issues masking team capability

### DORA Metrics Definitions and Measurement

**Deployment Frequency:**
- How often does your organization deploy to production?
- Measured: Number of deployments per day/week/month
- Improvement levers: CI/CD pipeline, feature flags, trunk-based development, smaller batch sizes

**Lead Time for Change:**
- Time from code commit to running in production
- Measured: Median time from first commit to production deployment
- Improvement levers: Automated testing, review automation, deployment pipeline speed

**Change Failure Rate:**
- Percentage of deployments that cause production incidents requiring rollback or hotfix
- Measured: Failures / Total deployments
- Improvement levers: Better testing, canary deployments, feature flags, staged rollouts

**Time to Restore Service (MTTR):**
- How long to recover from a production failure
- Measured: Median time from incident detection to resolution
- Improvement levers: Observability, runbooks, on-call culture, chaos engineering

### Beyond DORA: Developer Experience Metrics

**SPACE Framework (GitHub, 2021):**
- **Satisfaction**: Developer happiness, burnout risk, NPS
- **Performance**: Outcomes and impact (not code volume)
- **Activity**: Engineering outputs (PRs, reviews, deployments)
- **Communication & Collaboration**: How teams work together
- **Efficiency**: Flow state time, meeting load, context switching

**Developer surveys:**
- Run quarterly developer satisfaction surveys
- Measure: Tool quality, process friction, clarity of direction, psychological safety
- Separate: Am I satisfied? vs. Is the team high-functioning?

**Flow metrics (DORA + Planview):**
- Flow velocity: Business value delivered per time period
- Flow efficiency: Active time / (Active time + Wait time)
- Flow load: WIP items / team capacity
- Flow time: End-to-end delivery time for a feature

---

## Scaling Organizations

### Inflection Points: 10 → 100 → 1000 Employees

**10-person stage:**
- Everyone knows everything, informal communication works
- No process needed — process is a tax at this stage
- Founders in every important decision
- Single deployment pipeline, one product, one team

**10→50 (Forming specialization):**
- Hire first functional leads: VP Eng, Product Director, Sales leader
- Formalize: Weekly all-hands, team meetings, written decisions (FYI culture)
- First process: Incident response, code review standards, hiring process
- Architecture: First platform concerns emerge

**50→150 (Department formation):**
- Departments form with directors
- Two-pizza teams (Amazon): ~8 people per team max
- OKR system necessary — verbal alignment breaks down
- Culture becomes intentional (no longer default founder culture)

**150→500 (Dunbar's number broken):**
- Dunbar's number: ~150 people where everyone can know everyone
- Beyond 150: Cannot rely on relationships for coordination — need process
- Introduce: Engineering managers managing managers (Director → VP)
- Formalize: Leveling system, compensation bands, performance reviews
- Risk: Bureaucracy tax starts slowing decisions

**500→1000+ (Enterprise mechanics):**
- Federated structure: Multiple "mini-startups" inside the org
- Strategy → OKRs → Quarterly reviews becomes a full system
- Legal, Finance, HR become significant functions
- Acquisitions common as organic growth slows

### AI-Augmented Management (2025)
Per McKinsey 2025: Worker AI access up 50% YoY. AI is changing how managers operate:

**AI for managers:**
- Meeting summarization and action item extraction
- OKR drafting and progress tracking
- Job description generation and resume screening
- Performance review draft generation (manager reviews and personalizes)
- Budget variance analysis and forecasting

**AI management principles:**
- AI suggests, human decides (especially for people decisions)
- Transparency: Tell direct reports when AI helped draft feedback
- Calibration: AI feedback drafts require deep personalization — the generic 30% is obvious
- Skill preservation: Don't let AI atrophy managers' judgment

**AI team augmentation:**
- GitHub Copilot: 40-55% faster code completion tasks (GitHub internal data)
- Cursor: Full-file context coding, 2-4x developer speed on boilerplate
- AI code review: Automated first-pass review (Coderabbit, Qodo)
- Meeting assistants: Otter.ai, Fireflies, Zoom AI Companion

---

## Customer Success and Account Management

### CS Team Structure

**CS team ratios by segment:**
| Segment | ARR per Account | CSM:Customer Ratio |
|---------|----------------|-------------------|
| Enterprise | >$100K | 1:5-15 |
| Mid-market | $20K-100K | 1:30-80 |
| SMB | $5K-20K | 1:100-300 (tech-touch) |
| Self-serve | <$5K | 1:500+ (automated) |

**CS team roles:**
- **CSM (Customer Success Manager)**: Relationship owner, adoption, renewal
- **Implementation Specialist**: Onboarding, technical setup (60-90 day engagement)
- **Technical Account Manager (TAM)**: Deep technical advisory for enterprise
- **Renewal Manager**: Pure-play renewal focus at scale
- **CS Operations**: Data, tooling, process, reporting

### Health Scoring Framework

**Customer health score (0-100):**
```
Health Score = 
  (Usage score × 0.35) +
  (Engagement score × 0.25) +
  (Outcomes score × 0.20) +
  (Support score × 0.10) +
  (Financial score × 0.10)
```

**Usage score signals:**
- DAU/MAU ratio (target: >25% for SMB, >40% for enterprise)
- Feature adoption breadth (using core + expansion features)
- User seat utilization (contracted seats vs active)
- Frequency trend (week-over-week active user change)

**Risk tier thresholds:**
- Green (70-100): Healthy, renewal likely
- Yellow (40-69): Monitor, proactive outreach
- Red (0-39): Churn risk, escalate to management

### Net Revenue Retention (NRR) Excellence

NRR = (Starting ARR + Expansion ARR - Contraction ARR - Churned ARR) / Starting ARR

**NRR benchmarks (2025):**
- Below 90%: Significant problem — business is shrinking
- 100-110%: Solid — business grows from existing customers
- 110-120%: Strong — top quartile
- 120%+: Elite — Snowflake, Datadog-level. Expansions > churn + contraction

**Levers for improving NRR:**
1. **Reduce churn**: Better onboarding, health monitoring, QBRs, executive engagement
2. **Increase expansion**: Usage-based triggers, natural expansion in product, upsell motions
3. **Prevent downgrades**: Annual commit incentives, multi-year pricing, value ROI

**QBR (Quarterly Business Review) structure:**
- Business review: ROI achieved, metrics achieved, business outcomes delivered
- Product review: Feature usage, upcoming roadmap relevant to customer
- Strategic alignment: Customer's goals for next quarter, how we support them
- Action items: Clear owners, deadlines, follow-up mechanisms

---

## Build / Buy / Partner Decision Framework

### Decision Matrix

| Factor | Build | Buy | Partner |
|--------|-------|-----|---------|
| Core competency | Yes — build | No — buy | Maybe |
| Time to market | Slow (months-years) | Fast (weeks) | Medium |
| Customization need | High | Low-Medium | Depends |
| Vendor dependency risk | None | High | Medium |
| Ongoing cost | Engineering salaries | License fees | Revenue share |
| Competitive advantage | High | Low | Medium |

**Decision triggers:**
- **Build**: Core to your differentiation, existing solutions don't fit, you have the engineering capacity
- **Buy**: Not core, SaaS market has mature solutions, time-to-market critical
- **Partner**: Complementary to your core, distribution play, customer overlap

**Total Cost of Ownership (TCO) for build vs. buy:**
```
Build TCO = Engineering time × salary rate
           + Infrastructure cost
           + Ongoing maintenance (20-30% of initial build/year)
           + Opportunity cost (what else could engineers build?)

Buy TCO = License fees (annual)
         + Implementation cost
         + Integration engineering (typically 0.5-3x license cost)
         + Vendor lock-in risk premium
```

### Partnership Strategy

**Partnership types:**
1. **Technology partnerships**: APIs, integrations, marketplace presence (Stripe partner, HubSpot App Marketplace)
2. **Channel partnerships**: Resellers, VARs, MSPs, SI (System Integrators)
3. **Co-sell / OEM**: Co-sell agreements with larger vendors (AWS ISV Accelerate, Salesforce ISV)
4. **Strategic alliances**: Joint GTM, co-marketing, executive alignment

**Partnership success factors:**
- Mutual value: Both sides win (not just the bigger company)
- Clear ICP alignment: Same target customer
- Executive sponsor on both sides
- Co-sell motion with clear rules of engagement
- Regular cadence: Monthly partnership review

**Technology ecosystem strategy:**
- Build integrations with top tools in your customer's stack
- Priority: Native integrations > iPaaS (Zapier/Make) > open API
- "Better together" narrative: Your product + partner = more value than either alone
- Certified integration: Quality mark increases trust in partner marketplace

---

## Business Operations and Compliance

### SOX Compliance for Technology Companies
SOX (Sarbanes-Oxley Act) applies to public companies and companies preparing for IPO.

**2025 PCAOB updates:** Auditing Standard 2201 updated — stronger requirements for IT general controls.

**IT General Controls (ITGC) for SOX:**
- **Access controls**: Role-based access, privileged access management, access reviews (quarterly)
- **Change management**: Documented change process, separation of duties (dev ≠ deployment)
- **Backup and recovery**: Tested backup procedures, documented RTO/RPO
- **System availability**: Uptime monitoring, disaster recovery procedures

**Separation of duties (critical for SOX):**
- Developer cannot push directly to production (requires separate deployment role)
- Same person cannot write AND approve a change
- Financial system access requires dual approval

**Average SOX compliance cost (2025):** $2.3M/year for mid-market public company. Includes: external audit fees, internal audit team, control testing, remediation.

### Security Frameworks for Tech Companies

**SOC 2 Type II:**
- Trust Service Criteria: Security, Availability, Processing Integrity, Confidentiality, Privacy
- Type I: Point-in-time assessment
- Type II: 6-12 month operational effectiveness assessment (preferred by enterprise customers)
- Required by most enterprise procurement teams

**ISO 27001:**
- International standard for information security management systems (ISMS)
- Required by European enterprise customers and government contracts
- Certification cycle: Initial audit → Surveillance audits (annual) → Recertification (every 3 years)

**HIPAA for healthcare tech:**
- January 2025 Security Rule update: Stronger encryption requirements, annual risk assessments mandatory
- BAA (Business Associate Agreement) required with all vendors handling PHI
- Minimum necessary principle: Access only what's needed for the function

### Key Management Metrics

**Engineering efficiency metrics:**
- Revenue per engineer (benchmark: $350K-500K for Series B/C SaaS)
- Engineering cost as % of revenue (healthy: 15-25%)
- Cycle time per feature (planning → production)
- Technical debt ratio (estimated remediation cost / codebase value)

**People operations metrics:**
- Voluntary attrition rate (engineering: aim <15%, alarm >20%)
- Time-to-fill open roles (engineering: 45-60 days target)
- Offer acceptance rate (target: >85% at senior levels)
- eNPS (Employee Net Promoter Score, benchmark: >20)

**Customer-facing metrics:**
- Time to first value (TTFV): Days from contract to customer using product
- Onboarding completion rate: % completing defined onboarding steps
- Feature adoption: % of customers using key features in 90 days
- NPS: Net Promoter Score (benchmark: B2B SaaS median 32)
