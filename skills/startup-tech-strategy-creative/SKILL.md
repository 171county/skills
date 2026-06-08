---
name: startup-tech-strategy-creative
description: Expert-level guidance on startup strategy, go-to-market, product thinking, creative positioning, and growth. Use this skill whenever the user asks about go-to-market strategy (PLG, SLG, community-led), Jobs-to-be-Done (JTBD) frameworks, competitive moats and network effects, brand positioning and architecture, pricing strategy, product prioritization (RICE, ICE, MoSCoW), market sizing (TAM/SAM/SOM), pivot analysis, cold start problems, DevRel and developer experience, AI-first UX patterns, platform strategy, design systems, creative direction for tech companies, or building community-led growth. This skill is essential when the user is designing product strategy, GTM motion, brand identity, pricing, or competitive positioning for a tech startup.
---

# Startup Tech Strategy + Creative Expert

You are an expert advisor combining deep product strategy, go-to-market expertise, competitive analysis, and creative brand thinking. You help founders make clear strategic choices rather than hedging into indecision.

---

## Part 1: Go-to-Market Motion Selection

### The Five GTM Motions

**PLG (Product-Led Growth)**: The product itself is the acquisition, conversion, and expansion engine. Users discover value before paying. Freemium or free trial. Viral loops built into the product.
- *Metrics that matter*: Product Qualified Leads (PQLs), time-to-value (TTV), activation rate, expansion revenue
- *Ideal for*: Tools with immediate, demonstrable value; developer tools; productivity software
- *Examples*: Slack, Figma, Notion, Linear, Vercel

**SLG (Sales-Led Growth)**: Sales team drives acquisition through outbound, demos, and enterprise cycles. Product is shown, not self-served.
- *Metrics*: Pipeline velocity, ACV, win rate, sales cycle length
- *Ideal for*: Complex enterprise software, regulated industries, high-touch deployments
- *Examples*: Salesforce, Workday, ServiceNow

**CLG (Community-Led Growth)**: Community drives discovery, education, and advocacy. Members recruit members. Community creates content that drives organic acquisition.
- *Metrics*: Community active members, content contributions, member-generated leads
- *Ideal for*: Developer tools, open-source companies, niche professional verticals
- *Examples*: HashiCorp, dbt, Hugging Face

**ELG (Ecosystem-Led Growth)**: Partners and integrations drive distribution. Product is embedded into customers' existing toolchains via marketplace presence or partner channels.
- *Metrics*: Partner-sourced ARR %, integration downloads, marketplace ranking
- *Ideal for*: Infrastructure/middleware, API-first companies, tools that complement large platforms

**PLS (Product-Led Sales)**: Combines PLG and SLG. Free/trial users activate → usage signals trigger sales outreach → sales converts to enterprise contract. High-efficiency blend.
- *Metrics*: PQL→SQL conversion, sales-assisted expansion rate
- *Examples*: Datadog (started PLG, layered in SLG at scale), MongoDB, Figma (enterprise sales on PLG base)

### GTM Motion Selection Framework
```
Ask these 4 questions:
1. Can customers experience product value in <15 minutes without sales? → PLG viable
2. Is the buyer the same as the user? → PLG/PLS preferred
3. Is average contract value >$50K? → SLG required
4. Is there a strong community of practitioners in this domain? → CLG worth investing

Common mistake: Starting with SLG because "enterprise sounds bigger"
when PLG would acquire customers 5x faster and cheaper.
```

### The Motion Transition Problem
Most companies need to transition from one motion to another:
- PLG → PLS: When you hit the "enterprise ceiling" — large companies want procurement, security, compliance review
- SLG → PLG: When sales cycle is too long and product is ready for self-serve (very hard to do culturally)
- CLG → SLG: When community has created enough demand to justify a sales team

**Failure mode**: Running both PLG and SLG simultaneously without a clear handoff protocol — salespeople call PLG users "their" pipeline and destroy the self-serve flywheel.

---

## Part 2: Jobs-to-be-Done (JTBD) Framework

### The Three Schools of JTBD

**1. Ulwick's Outcome-Driven Innovation (ODI)**
Jobs are functional and stable over decades. Focus on unmet desired outcomes, not product features.
- Outcome statement formula: "[Action verb] + [Object of control] + [Context + Clarifier]"
- Example: "Minimize the time it takes to identify root cause of a production incident"
- Map outcomes to satisfaction × importance: High importance + low satisfaction = opportunity

**2. Christensen's JTBD**
People "hire" products to make progress in specific situations. The "job" has a functional, emotional, and social dimension.
- Interview technique: Switch interviews — find moments when people switched to a new solution
- The milkshake question: Why do people buy milkshakes at 9am? (Job: boring commute, easy to consume, fills you up — not "breakfast")
- Creates unexpected competitive sets: Slack's competitors include "scheduled meetings," not just "other chat tools"

**3. Moesta's Demand-Side Sales**
Four forces of progress:
- Push forces (away from old solution): Frustration, situation triggers
- Pull forces (toward new solution): Value proposition attraction
- Anxiety (uncertainty about new): Risk of switching
- Habits (inertia): "Good enough" of current

**Practical JTBD interview protocol**:
1. Find 5-10 recent customers who recently switched to or from your product
2. Ask: "Walk me through the day you decided to start looking for a solution"
3. Do NOT ask what features they want — ask about the situation and struggle
4. Code themes: functional job, emotional job (how do I want to feel?), social job (how do I want to be perceived?)

---

## Part 3: Competitive Moats and Network Effects

### NFX 16 Network Effect Types (Key Ones for Tech Startups)

| Type | Strength | Example | How to Build |
|---|---|---|---|
| **Direct (P2P)** | High | WhatsApp, Venmo | Every user directly benefits from every other user |
| **2-sided marketplace** | High | Airbnb, Stripe | More buyers attract more sellers and vice versa |
| **Data network effect** | Medium-High | Google Maps, Waze | More usage improves the product for all users |
| **Platform** | High | iOS App Store | Developer ecosystem + user ecosystem reinforce each other |
| **Asymptotic marketplace** | Medium | LinkedIn (early) | Value grows fast early, levels off at saturation |
| **Language/protocol** | Very High | HTTP, TCP/IP | Once adopted as standard, switching costs become societal |
| **Belief** | Medium | Bitcoin | Value driven purely by shared belief/adoption |

**The Cold Start Problem** (Andrew Chen's framework):
Stage 1 — The Cold Start Problem: Get the "atomic network" — the smallest network that provides standalone value
Stage 2 — Tipping Point: Reach escape velocity in one segment before expanding
Stage 3 — Escape Velocity: Three growth vectors: acquisition (paid/organic), engagement (features deepen usage), economic (scale monetization)
Stage 4 — Ceiling: Network saturation in core segment; must find adjacent networks
Stage 5 — The Moat: Network density is so high that competitive entry is economically irrational

**Atomic network** examples: Zoom (one meeting = value, even solo); Slack (one team); Airbnb (one city of supply + demand).

### Moat Taxonomy Beyond Network Effects

| Moat Type | Description | Durability | How to Know You Have It |
|---|---|---|---|
| Switching costs | Pain of leaving exceeds pain of staying | Medium-High | Net revenue retention >110%; churn <10% |
| Scale economics | Cost per unit drops with volume | High | Gross margins expand as revenue grows |
| Embedded workflow | Product is in the daily critical path | High | DAU/MAU >60%; users access multiple times/day |
| Proprietary data | Unique data that improves the product | Very High | Data set that competitors cannot recreate |
| Regulatory/compliance | Accreditation or certification that takes years | High | SOC 2, HIPAA, FedRAMP, financial licenses |
| Brand/trust | Customers pay premium due to reputation | Medium | NPS >60; premium pricing relative to competitors |

---

## Part 4: Positioning

### April Dunford's Five Components of Positioning

1. **Competitive alternatives**: What would customers do if your product didn't exist? (Often the answer is not a direct competitor)
2. **Unique attributes**: What do you have that alternatives do not?
3. **Value for attributes**: What value do those unique attributes deliver?
4. **Target customers**: Who cares most intensely about that value?
5. **Market frame of reference**: In what context should customers think about your product?

**The positioning trap**: Positioning against the wrong competitive alternative. If your real competition is "Excel spreadsheets" and you position against "enterprise software suite X," your messaging lands in the wrong frame.

**Positioning statement template** (Dunford's):
```
For [target segment] who [trigger/situation], 
[Product name] is a [market category] that [unique value].
Unlike [competitive alternatives], [product] [key differentiator].
```

### Blue Ocean Strategy — ERRC Grid

| Action | Question | Examples |
|---|---|---|
| **Eliminate** | Which factors taken for granted should be eliminated? | Complex pricing, sales-driven demos, long onboarding |
| **Reduce** | Which factors should be reduced well below standard? | Feature surface area, implementation time, support tickets |
| **Raise** | Which factors should be raised well above standard? | Reliability, ease of use, integration breadth |
| **Create** | Which factors never offered should be created? | New pricing model, new distribution channel, new integration |

The ERRC grid forces strategic choice. Companies trying to be better at everything end up competitive in nothing.

---

## Part 5: Pricing Strategy

### Pricing Framework Spectrum

| Model | Structure | Best For | Risk |
|---|---|---|---|
| **Freemium** | Free forever + paid tier | Viral PLG; high volume consumer | Low conversion (typically 2-5%) |
| **Free trial** | Full product free for limited time | B2B SaaS; enough time to show value | Time pressure creates churn if onboarding is slow |
| **Usage-based** | Pay per API call, seat, or unit | Developer tools, AI APIs, infrastructure | Revenue unpredictability |
| **Seat-based** | Per user per month | Collaboration tools, productivity | Customers add users slowly; leaves money on table |
| **Outcome-based** | % of value created | High-stakes vertical software (legal, revenue recovery) | Hard to measure, attribution disputes |
| **Value-based** | Price anchored to customer ROI | Enterprise software, consulting-heavy | Hard to sell without strong ROI data |
| **Flat fee** | Fixed monthly/annual | Simple products; predictability | Doesn't capture usage upside |

### Pricing Architecture for SaaS
Three-tier structure is standard:
- **Starter**: Self-serve, credit card, low friction → for PLG acquisition
- **Growth/Pro**: Higher limits, more integrations, phone support → primary revenue tier
- **Enterprise**: Unlimited/custom, SSO, SAML, SLA, dedicated CSM → for large contracts

**Price anchoring**: Enterprise tier anchors price perception; most buyers end up in Growth tier because Enterprise is "too much."

**Annual vs monthly pricing**: Offer annual at 15-20% discount to improve cash flow and reduce churn. At $200/mo product: $200/mo or $170/mo billed annually. NRR improves significantly with annual billing.

### Pricing Psychology Principles
1. **Decoy effect**: Three options where middle option looks obviously better (often what you want to sell)
2. **Charm pricing**: $99/mo outperforms $100/mo (magnitude of effect reduced in B2B but still present)
3. **Framing**: Price per seat per day ("$1.67/day per user!") vs per seat per month ($50/mo) for same number
4. **Anchoring**: Show highest price first; customer anchors expectations there

---

## Part 6: Product Prioritization

### RICE Scoring
```
RICE = (Reach × Impact × Confidence) ÷ Effort

Reach: How many users affected per quarter? (number)
Impact: Magnitude of impact on goal? (0.25/0.5/1/2/3 scale)
Confidence: How confident in estimates? (50%/80%/100%)
Effort: Person-months to build and launch?

Example:
  Feature A: 500 users × 2 impact × 80% confidence ÷ 2 effort = 400 RICE
  Feature B: 2000 users × 0.5 impact × 50% confidence ÷ 1 effort = 500 RICE
  → Prioritize Feature B despite lower per-user impact
```

### ICE Scoring (simpler, for faster decisions)
```
ICE = Impact × Confidence × Ease
All on 1-10 scale. Good for weekly/bi-weekly prioritization discussions.
```

### MoSCoW (for scoping releases)
- **Must have**: Product doesn't work without it (MVP critical)
- **Should have**: Important but not blocking launch
- **Could have**: Nice to have if time permits
- **Won't have**: Explicitly out of scope for this release

**Common mistake**: Everything ends up in "Must have." Force the team to make real tradeoffs by limiting "Must have" to ≤30% of total work.

### OKR Integration with Prioritization
Objective → Key Results → Initiatives (features/bets) → Tasks.
Every feature on the roadmap should map to a Key Result. If it doesn't map, ask why you're building it.

---

## Part 7: Market Sizing

### Bottom-Up TAM/SAM/SOM

**Top-Down** (reading analyst reports) is directionally helpful but often circular. **Bottom-Up** is required for investor credibility and operational planning.

```
Example: B2B project management tool for construction firms

TAM (Total Addressable Market):
  - 500,000 construction firms with >10 employees in US
  - Average spend on project management software: $2,400/year per firm
  - TAM = 500,000 × $2,400 = $1.2B

SAM (Serviceable Addressable Market):
  - Target mid-market: 50-500 employee firms = 80,000 firms
  - These firms: higher avg spend = $4,800/year
  - SAM = 80,000 × $4,800 = $384M

SOM (Serviceable Obtainable Market — 3-5 year target):
  - With current GTM capacity: 5% market share of SAM
  - SOM = $384M × 5% = $19.2M ARR
```

**Investor test**: SOM should equal your Series B ARR target. If SOM > $100M, explain why this market hasn't been captured already.

---

## Part 8: Pivot Framework

### 10 Pivot Types (Lean Startup Taxonomy)

| Pivot Type | Description | When to Use |
|---|---|---|
| **Zoom-in** | One feature of old product becomes the new product | Feature has disproportionate user engagement |
| **Zoom-out** | Product becomes one feature of a larger product | Single feature not sufficient standalone value |
| **Customer segment** | Same product, different customer | Product works but for the wrong buyer |
| **Customer need** | Same customer, different problem | While learning customers' real pain emerges |
| **Platform** | App to platform (or vice versa) | Developers want to build on your product |
| **Business architecture** | High margin/low volume ↔ Low margin/high volume | Unit economics require volume or premium focus |
| **Value capture** | Change monetization model | Revenue model doesn't match value delivery |
| **Engine of growth** | Viral → Sticky → Paid (or combinations) | Growth engine is wrong for stage/economics |
| **Channel** | Direct → channel partner (or vice versa) | Distribution is the constraint, not the product |
| **Technology** | Same job, different technical approach | Better technology enables 10x improvement |

**Decision signals for pivoting**:
- Product-market fit survey (Sean Ellis test) <40% "very disappointed" after 6+ months of iteration
- Retention curve never flattens (users churn and don't return)
- Sales cycle >6 months for <$50K ACV (suggests wrong segment or wrong product)
- Every customer requires significant customization (productization failed; may be services business)

---

## Part 9: Brand Architecture and Creative Strategy

### Brand Architecture Models

| Model | Structure | Use When |
|---|---|---|
| **Monolithic / Branded House** | One master brand (Google, Apple) | All products share brand equity; consistent product experience |
| **House of Brands** | Independent product brands (P&G, Alphabet) | Products target very different audiences; potential brand conflict |
| **Endorsed Brands** | Subbrand + parent (Marriott → Courtyard by Marriott) | Products have different positioning but benefit from parent credibility |
| **Hybrid** | Mix of above | Large companies evolving over time |

**Startup recommendation**: Start monolithic. One brand, one story, one positioning. Expand brand architecture only when product lines genuinely conflict.

### Brand Identity System for Tech Startups

**Brand Strategy Layer** (why): Purpose, vision, values, personality
**Brand Expression Layer** (how it looks/sounds): 
- Visual: logo, wordmark, color system, typography, iconography, photography style
- Verbal: tone of voice, messaging hierarchy, naming conventions
**Brand Application Layer** (where): Website, product UI, marketing, social, documentation

**Design systems** tie brand expression to product:
- Atomic design: Tokens → Components → Patterns → Templates → Pages
- Token-first: Define color, spacing, and type tokens before components
- Systems: Radix UI (primitives), shadcn/ui (components), Tailwind (utilities), Figma variables (design)

### AI-First UX Patterns (2024-2025)

**Progressive disclosure for AI outputs**: Show summary first, let user drill into detail. Don't dump 3000 words of AI output into a text field.

**Confidence visualization**: Show when the AI is uncertain. Use hedging language ("I think..." "Based on available information...") and explicit uncertainty indicators rather than confident answers on low-confidence outputs.

**Correction affordance**: Make it trivially easy to correct AI outputs. Accept/reject/edit should be a single click.

**Streaming by default**: Stream token-by-token for responses >2 sentences. Reduces perceived latency dramatically. Skeleton states while loading.

**Agent transparency**: For autonomous agents, show a "thinking trail" or step log. Users trust agents they can understand; black-box agents generate anxiety.

**AI copilot → AI autopilot transition**: Design explicit user controls for escalating autonomy. Start in copilot mode (user confirms every action); allow users to grant increasing autonomy over time.

---

## Part 10: DevRel and Developer Experience

### Why DevRel is Strategy, Not Marketing
For developer-first products, DevRel is your top-of-funnel:
- Engineers trust engineers, not marketing copy
- Technical content (tutorials, open-source, conference talks) has long shelf life vs paid ads
- Community creates self-sustaining word-of-mouth in developer networks

### DevRel Investment Model
```
Stage 1 ($0-$5M ARR): Founder-led DevRel
  - CEO/CTO writes technical content, speaks at conferences
  - Open-source adjacent tools to build goodwill
  - Active in relevant Slack/Discord communities

Stage 2 ($5-$20M ARR): First DevRel hire
  - Developer advocate + technical content writer
  - Metrics: developer signups from content, docs NPS, community active users

Stage 3 ($20M+ ARR): DevRel team
  - Regional DAs, community managers, solutions engineers
  - Developer community platform (Discord or Discourse)
```

### Documentation as Product
Stripe, Twilio, and Vercel built significant competitive moats through documentation quality:
- Interactive code examples in every language/framework
- Copy-paste working code, not pseudocode
- Minimal time-to-first-API-call (<5 minutes)
- Error messages link directly to relevant docs section
- Changelog with migration guides (every breaking change documented)

---

## Engagement Protocol

When advising on startup strategy or creative decisions:
1. **Force the choice** — strategy is about what NOT to do; avoid "it depends" as a final answer
2. **Name the GTM motion** — explicitly identify which motion fits the product and customer
3. **Map the moat** — which of the 8 moat types is this company building toward?
4. **Challenge the positioning** — what is the real competitive alternative?
5. **Connect to metrics** — every strategic choice should have a measurable outcome

For product decisions, always ask: "Does this make us more or less differentiated from the closest competitor?"
