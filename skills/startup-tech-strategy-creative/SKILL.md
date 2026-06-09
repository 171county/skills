---
name: startup-tech-strategy-creative
description: Expert-level startup strategy and creative execution knowledge covering customer discovery (Jobs-to-be-Done, JTBD interviews), product-market fit (Sean Ellis test, PMF signals), MVP strategy (types and build decisions), go-to-market motions (PLG, SLG, PLS hybrid), competitive strategy (Porter's Five Forces, Blue Ocean ERRC), positioning (April Dunford's 5 components), brand building for tech startups, AI differentiation strategies, pricing models (value-based, usage-based, tiered), growth loops, SPADE decision framework, and creative product strategy. Use for any startup strategy, GTM, positioning, brand, or creative product development question.
---

# Startup Tech Strategy + Creative Expert

## Customer Discovery

### Jobs-to-be-Done (JTBD) Framework

Tony Ulwick and Clayton Christensen's insight: Customers don't buy products—they **hire** products to do a job in their lives. Understanding the job better than anyone else is the foundation of durable competitive advantage.

**Three types of jobs:**
1. **Functional job:** The practical task (send a message, analyze data, track expenses)
2. **Emotional job:** How they want to feel (confident, in control, impressive to peers)
3. **Social job:** How they want to be perceived (competent, innovative, responsible)

Most products compete only on functional jobs. Winning products nail all three.

**JTBD interview protocol (Bobrow's format):**

```
Pre-interview setup:
- Find people who recently did what your product does (hired a solution in the last 3-6 months)
- Don't pitch; NEVER show your product during the interview
- 30-45 min; record with permission

Opening (5 min):
  "Can you walk me through the last time you [did the thing we're interested in]?"

Timeline construction (15 min):
  "What triggered you to start looking into this?"
  "How long had that been a problem before you started looking?"
  "What were you doing before this solution?"
  "What did you search for? Who did you talk to?"
  "What other solutions did you consider?"
  "What almost stopped you from making a decision?"
  "Why did you ultimately choose [solution they chose]?"

Progress-making (10 min):
  "What were you hoping to achieve by solving this?"
  "What does success look like 6 months from now?"
  "What would you tell someone else who's in the situation you were in?"

Forces (5 min):
  Four forces that drive switching decisions:
  - PUSH: What frustrated you about the old solution?
  - PULL: What attracted you to the new solution?
  - ANXIETY: What almost stopped you from switching?
  - HABIT: What made it hard to change?
```

**Anti-patterns in customer discovery:**
- Asking about hypothetical features ("Would you use X?")—people say yes to everything
- Asking about the future ("Would you pay $X?")—hypothetical ≠ actual behavior
- Validating rather than learning—going in with the answer you want
- Interviewing friends/family—too polite to give critical feedback
- <10 interviews before drawing conclusions

**Rob Fitzpatrick's "Mom Test":** Make questions so good that even your mom can't lie to you. Focus on past behaviors, current pain, actual spending. Never ask if they'd use your product.

---

## Product-Market Fit

### Sean Ellis Test (The 40% Rule)

Survey your most active users: **"How would you feel if you could no longer use [product]?"**

Options: Very disappointed / Somewhat disappointed / Not disappointed / N/A (I no longer use it)

**Interpretation:**
- ≥40% "Very disappointed" = Have PMF (strong signal)
- 25–40% = Getting close; identify what the 40%+ segment looks like and double down on them
- <25% = No PMF; pivot required

**Critical nuance:** Survey only users who have experienced the core value proposition—not trial users, not recently acquired users. Typically users who have used the product ≥2 times in the past 2 weeks.

**Segment analysis:** Even with overall <40%, look for segments with ≥40%. That's the wedge. Build the product for that segment first, then expand.

### PMF Signals Across the Stack

| Signal | Weak | Strong |
|--------|------|--------|
| Retention | Users drop off after week 1 | 40%+ retained at 8 weeks |
| NPS | 0–20 | 40+ |
| Organic growth | 0–5% from referrals | 20%+ from referrals |
| Sales cycles | Long, heavy objections | Short, users come inbound pre-sold |
| Support | "How do I..." questions | "When can I have more..." requests |
| Expansion | Rare | Users naturally upgrade, add seats |
| Engagement | Sporadic, need prompting | Habitual, users complain when you change things |

**Brian Balfour's PMF Equation:**
PMF is achieved when: Market fit + Product fit + Channel fit + Model fit all align. Each must fit the others—a great product in the wrong market through the wrong channel at the wrong price will fail even with product-market fit by the Sean Ellis measure.

### Pivot Decision Framework

**When to persist:** Strong retention, qualitative love from a segment, team still has learning hypotheses to test, competitive insight not yet fully explored.

**When to pivot:** No segment has ≥40% very disappointed after 20+ interviews, churn is >10% monthly with no improving trend, core assumption was wrong, better opportunity identified adjacent to current product.

**Types of pivots (Eric Ries):**
- Zoom-in pivot: One feature becomes the product
- Customer segment pivot: Same product, different customer
- Problem pivot: Same customer segment, different problem
- Platform pivot: Application → platform
- Business architecture pivot: High margin few customers ↔ Low margin high volume

---

## MVP Strategy

### MVP Type Selection

| MVP Type | What It Tests | Time/Cost |
|----------|--------------|-----------|
| Smoke test (landing page + waitlist) | Demand signal, messaging resonance | Hours–days |
| Concierge (manual delivery of the product value) | Whether customers want the outcome | Days–weeks |
| Wizard of Oz (appears automated, is manual) | Whether AI/automation approach works | Days–weeks |
| Prototype (InVision, Figma) | UX flows, navigation assumptions | 1–2 weeks |
| Spike (technical feasibility proof) | Whether the core technical challenge is solvable | 1–2 weeks |
| Single-feature MVP | Core loop only; ship it | 4–8 weeks |

**Choose the cheapest test that gets the decision-quality information you need.** Don't build a full product to test a hypothesis a landing page can answer.

**MVP anti-patterns:**
- Building for edge cases before core loop works
- MVP that takes >3 months to ship (too much investment before signal)
- Perfecting design before getting usage data
- Including features users asked for rather than ones that test your core hypothesis

### What to Cut

Using MoSCoW, cut ruthlessly to the MUST HAVEs:
- Notifications (can send manual emails)
- Settings/preferences (hardcode defaults)
- Admin dashboard (use spreadsheet + Metabase)
- Onboarding flow polish (do it manually over Zoom calls)
- Multiple payment plans (start with one)
- Integrations (none for V1 unless they're core to the value prop)

**Jeff Bezos's 1-way vs. 2-way doors:** Before cutting a feature, ask: is this a reversible decision (2-way door)? If so, cut it and add it back later if users complain. If irreversible (like deleting a database), be careful.

---

## Go-to-Market Motions

### Product-Led Growth (PLG)

Users discover, try, and adopt the product independently before (or instead of) buying. Growth comes from product virality and word-of-mouth.

**PLG mechanics:**
- Free tier or free trial creates low-friction entry
- Product delivers value before asking for payment (Slack: use free → hit limits → upgrade)
- Built-in virality: every collaboration feature spreads the product (Notion, Figma, Loom)
- Self-serve upgrade: no sales rep required; payment is in-product
- **PQL (Product Qualified Lead):** User actions that predict conversion (sharing a Loom video, inviting 5+ teammates, hitting usage limits)

**PLG activation metrics:**
- Time to first "aha moment" (Slack: team sends first message)
- Day-1, Day-7, Day-30 activation rates
- Conversion rate free → paid by cohort
- Expansion revenue from self-serve

**When PLG works:** Product delivers standalone value quickly; target users are developers or prosumers; problem is well-understood enough to self-serve.

**When PLG fails:** Enterprise compliance/security requirements; long-horizon value (ROI not visible until months later); complex change management required; product requires training to see value.

### Sales-Led Growth (SLG)

Enterprise sales motion: Account Executives, SDRs, sales development, relationship-based.

**SLG mechanics:**
- Outbound SDR prospecting → booked demos → AE qualification → negotiation → close
- Relationship-based enterprise deals: navigate multiple stakeholders, procurement, legal
- Custom demos, POCs (proof of concepts), RFP responses
- Long cycles: 3–18 months for enterprise deals

**Key SLG metrics:**
- Pipeline coverage ratio: 3–4× quota in qualified pipeline
- Win rate: Qualified opps closed (25–35% is strong enterprise)
- Average sales cycle: Benchmark vs. comparable ACV deals
- CAC payback period (see business-finance-expert skill)
- Quota attainment: % of reps hitting quota (healthy = 60–70%)

### Product-Led Sales (PLS / PLG + SLG Hybrid)

The dominant motion for modern B2B SaaS. Product creates awareness and initial users; sales intervenes at the right moment to expand deals.

**PLS motion:**
1. Individual user self-serves → uses free or trial
2. User demonstrates value internally → invites colleagues
3. Usage hits threshold → PQL signal fires
4. Sales rep reaches out with context from product usage
5. Expand to department/enterprise contract

**PQL implementation:**
```python
# PQL scoring based on product usage signals
def calculate_pql_score(user_id: str) -> float:
    usage = get_user_metrics(user_id)
    
    signals = {
        "invited_teammates": 3.0 if usage.invites > 2 else 0,
        "hit_usage_limit": 5.0 if usage.hit_limit else 0,
        "shared_externally": 4.0 if usage.external_shares > 0 else 0,
        "daily_active_user": 2.0 if usage.days_active_last_7 > 5 else 0,
        "high_value_feature_used": 3.0 if usage.used_premium_feature else 0,
        "multiple_sessions": 1.5 if usage.sessions_last_30 > 10 else 0,
    }
    
    score = sum(signals.values())
    if score >= 8:
        trigger_sales_alert(user_id, score, signals)
    return score
```

---

## Competitive Strategy

### Porter's Five Forces

Used to analyze industry attractiveness and competitive dynamics:

1. **Threat of new entrants:** Barriers to entry (capital requirements, switching costs, network effects, regulatory approval, brand loyalty). Low barriers → competitive pressure.

2. **Bargaining power of suppliers:** Are your key inputs (cloud, LLM APIs, data) from one or few providers? High supplier power → margin compression.

3. **Bargaining power of buyers:** Can customers easily switch? Are they price-sensitive? Concentrated buyers (3 customers = 80% of revenue) = very high buyer power.

4. **Threat of substitutes:** Not just direct competitors, but different solutions to the same job. Spreadsheets substitute for SaaS project management. High threat of substitutes = price ceiling.

5. **Competitive rivalry:** Number of competitors, industry growth rate, exit barriers, product differentiation. Commoditized markets with many players = low margins.

**Action from Five Forces:** Seek markets where barriers to entry are rising (not falling), suppliers are fragmented, buyers are price-inelastic, substitutes are costly/inferior, and rivalry is limited by differentiation.

### Blue Ocean Strategy (W. Chan Kim, Renée Mauborgne)

**Compete in "red oceans"** = fight over existing demand in known markets (turns ocean red with blood of competition).

**Create "blue oceans"** = create new uncontested market space, making competition irrelevant.

**ERRC Grid (Eliminate-Reduce-Raise-Create):**
```
ELIMINATE: Which factors does the industry take for granted that should be eliminated?
  → Cirque du Soleil eliminated animals, star performers, multiple arenas

REDUCE: Which factors should be reduced well below the industry standard?
  → Cirque reduced slapstick humor, aisle concessions

RAISE: Which factors should be raised well above the industry standard?
  → Cirque raised unique venue, artistic music, refined environment

CREATE: Which factors should be created that the industry has never offered?
  → Cirque created a theme, multiple productions, artistic dance
```

**Value innovation:** Simultaneously reduce costs AND increase buyer value—not a trade-off. This is the blue ocean breakthrough that traditional strategy misses.

---

## Positioning

### April Dunford's 5-Component Positioning Framework

From *Obviously Awesome* (2019). The definitive framework for tech startup positioning.

**1. Competitive alternatives:**
What would customers do if your product didn't exist? (Often: Excel, hiring a person, doing nothing.)

**2. Unique attributes:**
What do you have that alternatives don't? (Features, data, team, integrations, speed.)

**3. Value:**
So what? For each unique attribute, what value does it deliver to the customer? Attributes alone don't sell—value does.

**4. Target customer:**
Who values this specific combination of attributes highly? Not "enterprises" or "SMBs"—specific personas in specific situations with specific goals.

**5. Market frame of reference:**
What market context makes your value obvious? This is the category you put yourself in—which sets the comparison set in the customer's mind.

**Positioning statement template:**
> "For [target customer] who [has the problem/goal], [product name] is a [market frame] that [primary value]. Unlike [competitive alternative], our product [key differentiator]."

**Positioning process:**
1. Find your best customers (highest retention, highest NPS, highest expansion)
2. Identify what they have in common (job title, company stage, specific workflow)
3. Interview them about why they chose you over alternatives
4. Find the patterns—what do they consistently cite as unique value?
5. Build the category frame that makes that value obvious to similar buyers

---

## Brand Building for Tech Startups

### Brand ≠ Logo

Brand is the sum total of associations people make with your company: your reputation, the emotions triggered by your name, the expectations your product creates.

**Brand elements:**
- **Name:** Memorable, distinct, available as domain and trademark
- **Voice:** How you communicate (technical/casual, authoritative/humble, warm/direct)
- **Visual identity:** Color, typography, photography style, illustration style
- **Story:** Founder origin story, why this problem, why now, why you
- **Promise:** What customers should always be able to count on you for

### Category Creation vs. Category Entry

**Category entry:** "We're like [established product] but [better/cheaper/easier]."
- Faster to explain; inherit existing buyer education
- Always positioned as inferior to category leader

**Category creation:** "We're a [new category name]."
- Requires more education (expensive)
- First mover advantage in positioning; own the category
- Examples: "The Business Intelligence platform," "The Observability company," "Conversational AI"

**When to create a category:** When your product genuinely has no direct competitor; when existing categories actively mislead buyers about your differentiation; when the category leader's product is fundamentally different from yours.

### Narrative Architecture

**The product story arc that works:**

```
1. The world used to work like this... [status quo]
   "Software teams used to manage work in spreadsheets and weekly standup meetings..."

2. Then something changed... [market shift]
   "Remote work distributed teams across time zones..."

3. As a result, a new set of problems emerged... [new problem]
   "And suddenly, nobody knew what anyone else was working on..."

4. Existing solutions don't solve the new problem... [gap]
   "PM tools built for co-located teams created overhead, not clarity..."

5. We built something new... [your product]
   "Linear is asynchronous by design..."

6. And here's the proof it works... [social proof + outcomes]
```

This narrative works for pitch decks, sales pages, investor updates, and product marketing.

---

## Pricing Strategy

### Value-Based Pricing

Price based on the value delivered to the customer, not your costs.

**Value-based pricing process:**
1. Identify the "value metric" (what your product helps customers achieve or avoid)
2. Quantify the value in customer terms (time saved × hourly rate, risk reduced × probability × cost)
3. Price at 10–20% of value delivered (leaves 80–90% value surplus with customer, creating strong ROI)
4. Segment by willingness to pay across customer types

**Value metric examples:**
- Seats (Salesforce, Slack) — scales with org size
- API calls (Stripe, Twilio) — scales with usage
- Records/users managed (HubSpot CRM) — scales with data volume
- Revenue processed (Stripe) — aligns with customer success
- Data analyzed (Snowflake) — scales with workload

### Tiered Pricing (Good/Better/Best)

**Three-tier structure:**
```
STARTER    PROFESSIONAL    ENTERPRISE
$29/mo     $99/mo          Custom
For        For teams       For large
individuals that            orgs with
           collaborate     compliance
                           needs
Most popular: PROFESSIONAL (anchor effect)
```

**Psychological anchoring:** The middle tier should be your primary conversion target. The high tier (Enterprise) makes the middle look reasonable. The low tier (Starter) reduces risk perception.

**Feature gate strategy:** Don't gatekeep core value (customers can't see it). Gate collaboration, admin controls, compliance features, advanced analytics—things only teams/enterprises need.

### Usage-Based Pricing (UBP)

Charges per unit of value delivered rather than a flat subscription. Increasingly common in AI products.

**Benefits:** Customers start small (reduced friction), grow as they get value (natural expansion), aligns vendor and customer incentives.

**Risks:** Revenue unpredictability, high-volume customers negotiate hard, CAC payback extended if customers ramp slowly.

**Hybrid approach:** Minimum commitment + usage above threshold:
- "$500/month minimum (covers 50,000 API calls), $0.01/call above that"
- Provides revenue predictability + unlimited upside

---

## Growth Loops

Growth loops are self-reinforcing mechanisms where growth creates the inputs for more growth. More powerful than funnels (linear, one-and-done).

### Types of Growth Loops

**Viral loops:**
- User gets value → Invites others → Others get value → Invite others
- Examples: Dropbox (share a file → recipient needs Dropbox), Calendly (send a link → recipient signs up), Loom (share video → viewer wants to create loom videos)

**Content loops:**
- Create content → Content drives SEO/awareness → New users find content → Become users → Create more content
- Examples: Pinterest (users pin → pins indexed by Google → Google traffic → new pinners), GitHub (code repos indexed → developers find repos → create repos → more content)

**Community loops:**
- Users get value → Contribute to community → Community attracts more users → More contributions
- Examples: Stack Overflow, Product Hunt, Figma Community

**UGC/Marketplace loops:**
- Supply creates demand → Demand attracts supply → Better supply attracts more demand
- Examples: Airbnb, YouTube, Shopify

### Building Your Growth Model

```python
# Simple growth loop model
def model_growth_loop(initial_users: int, months: int,
                       viral_coefficient: float,  # invites per user × acceptance rate
                       monthly_churn_rate: float,
                       organic_acquisition: int) -> list[int]:
    """
    viral_coefficient = K-factor. K > 1 = exponential viral growth.
    Typical range: 0.1 (weak viral) to 0.8 (strong viral). K > 1 is rare.
    """
    users = [initial_users]
    for month in range(months):
        current = users[-1]
        viral_new = int(current * viral_coefficient)
        remaining = int(current * (1 - monthly_churn_rate))
        next_month = remaining + viral_new + organic_acquisition
        users.append(next_month)
    return users
```

---

## SPADE Decision Framework

For high-stakes, reversible or irreversible strategic decisions where alignment matters.

**S — Setting:** Context and why this decision matters now. Time pressure, opportunity cost of delay.

**P — People:** Who is the Responsible decision-maker? Who is Accountable (signs off)? Who is Consulted? Who is Informed? (Avoid decision by committee—one decision-maker.)

**A — Alternatives:** 3+ real options, not straw-man alternatives. Include "do nothing" as an option.

**D — Decide:** Use a decision matrix or explicit criteria. Recommendation should flow logically from alternatives analysis.

**E — Explain:** After deciding, write a decision doc explaining the reasoning. Creates alignment, documents assumptions, enables future review if context changes.

**When to use SPADE:** Major pivots, pricing changes, entering new markets, significant architectural decisions, hiring above a threshold (Director+), M&A considerations.

---

## AI-Specific Differentiation Strategies

### The AI Moat Stack

Building defensibility in AI products requires stacking multiple moats:

| Moat Type | How to Build | Durability |
|-----------|-------------|------------|
| **Proprietary data** | Unique training data from product usage, partnerships, user-generated | High |
| **Workflow integration** | Embed deeply in users' daily workflow; create switching costs | High |
| **Network effects** | Each user makes the product better for others (collaborative AI, benchmark data) | Very high |
| **Brand/trust** | AI safety, accuracy reputation, brand recognition | High |
| **API ecosystem** | Developers build on your API; deprecation costs for them | Medium |
| **Speed/model quality** | Frontier model advantage (erodes quickly—training is replicable) | Low |

**The feature trap:** Competing solely on AI model quality (speed, accuracy) with no proprietary data or workflow moat is a losing strategy against foundation model providers. Add your unique data layer or workflow integration.

### Data Flywheel Strategy

```
Users use product → Generate interaction data →
Fine-tune/improve model on data → Better model →
More users attracted → More data generated → ...
```

Build data flywheels explicitly:
1. What user actions generate valuable feedback data?
2. How do you capture implicit feedback (time spent, corrections made, files shared)?
3. How do you use that data to improve the model?
4. Does improvement attract more users who generate better data?

---

## Quick Reference

**Customer discovery** → JTBD interviews, Mom Test, 20+ interviews before drawing conclusions

**PMF** → 40% "very disappointed" test, retention signals, organic referral rate

**Positioning** → Dunford's 5 components: alternatives → attributes → value → target → frame

**GTM motion** → PLG (product-led, self-serve), SLG (enterprise sales), PLS (hybrid: product creates lead, sales expands)

**Pricing** → Value-based > cost-plus; three-tier structure; value metric that scales with customer success

**Growth loops** → Viral, content, community, marketplace loops > linear funnels

**AI differentiation** → Proprietary data + workflow integration + network effects > model quality alone

**SPADE** → For high-stakes decisions: Setting, People, Alternatives, Decide, Explain
