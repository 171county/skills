---
name: startup-tech-ai-agentic-expert
description: Expert-level knowledge base for building AI-first agentic startups. Use this skill when users ask about agentic AI architecture, multi-agent systems, AI startup strategy, agentic frameworks (LangGraph, CrewAI, AutoGen, OpenAI Agents SDK, Claude Agent SDK, Google ADK), agent memory, production deployment, AI startup fundraising, go-to-market for agentic products, competitive moats, team composition, or how to build and scale autonomous AI agent systems. Invoke for any question about building production-grade agentic AI systems, vertical AI startups, or launching AI-first companies.
---

# Startup Tech AI Agentic Expert

Expert-level knowledge for building, scaling, and fundraising AI-first agentic startups (2024–2026).

---

## 1. What Makes a Startup "AI-First" in the Agentic Era

An AI-first startup in the agentic era is defined by architecture and value delivery — not by whether it uses AI.

**True AI-first markers:**
- **The product is the agent.** Core value is delivered by an autonomous system that takes multi-step actions. Examples: Harvey's legal agents autonomously draft and review documents; Sierra's agents autonomously resolve customer tickets end-to-end.
- **Outcome-based value proposition.** Sells outcomes ("resolved tickets," "contracts reviewed") not features or access.
- **Workflow integration, not widget.** Integrates into existing SaaS ecosystems (Salesforce, Workday, Jira), executes multi-step processes, and persists state across sessions.
- **Human-in-the-loop by design.** Audit trails, confidence thresholds, and escalation paths are features — not failure modes.
- **Data flywheel from day one.** Every task execution generates proprietary feedback data that improves the agent, compounding over time.

**The agentic maturity arc (2023–2026):**
- 2023: ReAct loops with web search
- 2024: Multi-step tool use with state persistence
- 2025: Multi-agent systems, sub-agent delegation, durable long-running workflows, human escalation
- 2026: Autonomous agents with computer use, cross-application orchestration, verifiable outcomes

YC S25 batch: 80%+ AI companies; ~33% building autonomous agents that "execute complete workflows, not just assist humans."

---

## 2. Core Agentic AI Frameworks

### LangGraph
- **Architecture:** Directed graph where nodes are agent steps and edges are conditional transitions. Allows cycles (looping). Built on LangChain.
- **State management:** Built-in checkpointing with time-travel replay. States persist across steps, agents, and retries.
- **Strengths:** Best production readiness. Lowest latency in benchmarks. First-class human-in-the-loop (interrupt nodes). Excellent audit trail.
- **Weaknesses:** Steeper learning curve; more boilerplate than smolagents/CrewAI.
- **Best for:** Enterprise production deployments requiring state control, rollback, and compliance.

### CrewAI
- **Architecture:** Role-based "crews." Agents defined by role, goal, backstory. Two process types: Sequential (pipeline) and Hierarchical (manager delegates to workers).
- **Strengths:** Lowest learning curve (~20 lines to start). Excellent for multi-agent collaboration with clear role separation.
- **Weaknesses:** Less flexible than LangGraph for complex branching; limited native checkpointing.
- **Best for:** Multi-agent collaboration, content generation pipelines, research workflows.

### AutoGen / AG2
- **Architecture:** v0.4 rewrite (now "AG2") is event-driven async-first. GroupChat: multiple agents in a shared conversation with selector determining who speaks next.
- **Strengths:** Best for research, complex multi-agent conversations, academic prototyping.
- **Weaknesses:** 4-agent debate with 5 rounds = minimum 20 LLM calls. Too expensive for high-volume production. No persistence by default.
- **Best for:** Research, debate/verification patterns, academic multi-agent scenarios.

### OpenAI Agents SDK (released March 2025)
- **Architecture:** Four primitives: agents, handoffs, guardrails, built-in tracing. Handoff = explicit control transfer with conversation context.
- **Strengths:** Production-grade, minimal abstractions, excellent for OpenAI-native deployments. Efficient token usage.
- **Weaknesses:** Tightly coupled to OpenAI models; less flexible for multi-provider.
- **Best for:** Teams committed to OpenAI stack, need rapid production deployment.

### Anthropic Claude Agent SDK
- **Architecture:** Tool-use chains with sub-agent delegation. Core design: "give the agent a computer" — OS-level access enabling computer use (browser, terminal, file system).
- **Unique strengths:** Computer use (vision + action), MCP (Model Context Protocol) native support, safety-first design.
- **Best for:** Developer assistants, "agentic computer" paradigm.

### Google ADK (released April 2025)
- **Architecture:** Hierarchical agent tree — root agent delegates to sub-agents recursively. Native A2A (Agent-to-Agent) protocol for cross-framework communication.
- **Unique strengths:** Multimodal (text, images, audio, video) via Gemini API. A2A enables ADK agents to discover/invoke LangGraph or CrewAI agents. Multi-language (Python/Java/Go).
- **Best for:** Google Cloud deployments, multimodal workflows, cross-framework interop.

### smolagents (Hugging Face)
- **Architecture:** Minimalist, code-first. Agent writes and executes Python to achieve goals rather than calling predefined tools.
- **Strengths:** ~40 lines for a ReAct agent. Perfect for rapid prototyping, code agents.
- **Best for:** Rapid prototyping, learning, code generation agents.

### Agno (formerly phidata)
- Speed-first design, clean API. Includes AgentUI and AgentOS. Good for rapid prototyping that needs production scale.

### Framework Selection Decision Matrix

| Need | Recommended |
|---|---|
| Production, audit trail, rollback | LangGraph |
| Multi-role agent collaboration | CrewAI |
| Research / complex debate | AutoGen/AG2 |
| OpenAI-native, fast ship | OpenAI Agents SDK |
| Computer use / developer tools | Claude Agent SDK |
| Google Cloud / multimodal | Google ADK |
| Rapid prototype / code agent | smolagents |
| Speed + enterprise UI | Agno |

---

## 3. Multi-Agent Architecture Patterns

### Orchestrator/Worker
- Single orchestrator receives task, decomposes it, dispatches to specialized worker agents, aggregates results.
- Orchestrator maintains state; workers are stateless and replaceable.
- Best for: document processing pipelines, research synthesis, data extraction.

### Supervisor Architecture
- Supervisor monitors worker outputs, validates quality, routes failures for retry or escalation.
- Adds verification without full human review.
- Best for: customer-facing workflows requiring guaranteed output quality.

### Hierarchical Agents
- Nested delegation: Orchestrator → Manager agents → Worker agents.
- Google ADK's native pattern.
- Best for: large enterprise workflows with complex decision trees.

### Peer-to-Peer / A2A
- Agents communicate directly via a shared protocol (Google A2A, Anthropic MCP).
- Best for: multi-team deployments where different vendors own different agents.

### Architecture Principles for Startups
1. Start with a single-agent loop before adding orchestration. Premature orchestration multiplies cost and failure surface.
2. Make each agent tool-complete — all tools needed without calling back to orchestrator.
3. Idempotent tool calls — deterministic key = (workflow_id + step_index + action_type) prevents double-execution on retry.
4. Checkpoint at every state transition using LangGraph checkpointing or Temporal.
5. Define explicit handoff contracts with typed schemas, not free-text, at agent boundaries.

---

## 4. Go-to-Market for Agentic Products

### PLG vs. Enterprise Sales

**PLG works when:**
- Agent delivers immediate, self-evident value in minutes (coding assistants, writing tools).
- Typically developer-first: individual sign-up → team adoption → enterprise contract.
- Risk: High churn if agent fails on early tasks.

**Enterprise Sales required when:**
- Agent touches regulated data (healthcare, legal, finance).
- Requires custom integrations or compliance review.
- Sales cycle: 3–9 months with security review, legal, pilot.

**Hybrid (most common 2025):** Free tier → SMB self-serve → enterprise upsell. PLG generates proof points for enterprise sales.

### Pricing Models

**Per-Task / Outcome-Based (fastest growing):**
- Sierra model: pay per "completed ticket resolution," not per seat.
- Harvey: ACV tied to deals completed, documents reviewed.
- Expected ~30% adoption by 2026 (from near-zero in 2022).
- Do NOT deploy until task success rate is measurable and >90% consistent.

**Usage-Based (mainstream — 61% adoption 2024):**
- Per API call, per token, or per workflow run.
- Predictable for customers at scale once usage patterns stabilize.
- Best for infrastructure-layer companies.

**Per-Seat (declining for pure agents):**
- Still relevant when each employee has their own AI copilot.
- Hybrid: base seat fee + usage overage.

**Hybrid (most defensible):**
- Base platform fee (predictability) + per-task fee (value alignment).

**Bessemer insight:** Pricing is the foundation of GTM strategy. Outcome-based pricing requires confidence in task completion reliability — gate it behind proven TSR.

---

## 5. Agent Memory Systems

### Short-term (Working Memory)
- Current conversation/task context window.
- Risk: Token overflow on long tasks.
- Mitigation: Sliding window with summarization; retrieve-only-relevant pattern.

### Long-term Semantic Memory
- Persistent facts about users, entities, preferences.
- Stored in vector databases (Pinecone, Weaviate, pgvector).
- Retrieved via semantic search at each turn.

### Episodic Memory
- Records of past task executions — what happened, what worked.
- Enables agents to learn from past failures without fine-tuning.
- **Mem0** (managed, 80% token reduction) and **Zep** (Temporal Knowledge Graph, LongMemEval: 63.8% vs Mem0's 49.0%) are leading production solutions.

### Procedural Memory
- How to do things — stored as tool definitions, workflow templates, code.
- Updated through few-shot examples appended to system prompts.

### Context Management Strategies
- Sliding window with summarization (oldest turns compressed)
- Retrieval-augmented context (fetch only relevant prior context)
- Structured state objects (extract only necessary state, discard raw transcripts)
- Token budget management: hard limits per agent call

---

## 6. Key Metrics

### Operational Metrics

| Metric | Definition | Production Target |
|---|---|---|
| Task Success Rate (TSR) | % of tasks completed correctly end-to-end | >85% launch; >95% at scale |
| Human Intervention Rate | % of tasks requiring human review | <15% target; track trend |
| Cost per Task | Total LLM + infra cost / tasks completed | Must show positive cost/value ratio |
| Tool Hallucination Rate | % of tool calls with wrong parameters | <5% for reliability |
| Task Latency P50/P95 | Time to completion | SLA-driven |
| Retry Rate | % of tasks requiring retries | Leading indicator for reliability |

### Business Metrics

| Metric | Investor Benchmark (Series A) |
|---|---|
| ARR | $1–3M+ |
| NRR | >120% signals strong PMF |
| CAC Payback Period | <18 months |
| Agent Utilization Rate | High = healthy; low = churn risk |
| Outcome Delivery Rate | Core SLA for outcome-based pricing |

---

## 7. Production Deployment Patterns

### Async Task Queue Architecture (the correct default)
- HTTP request → task ID returned immediately → task queued → agent executes in background → webhook on completion.
- Never tie agent execution to the originating HTTP connection (timeout will kill long-running agents).
- Stack options: Celery + Redis, BullMQ + Redis (Node.js), Temporal (durable execution), AWS SQS + Lambda.

### Durable Execution with Temporal
- For agents running hours/days that must survive infrastructure failures.
- Automatic checkpointing, built-in retry, state persistence.
- Use when: agent tasks exceed 30 seconds, involve external API calls with latency, or must survive pod restarts.

### Error Recovery Patterns
- **Exponential backoff with jitter:** 1s → 2s → 4s → … → 60s cap, ±20% jitter to prevent thundering herd.
- **Circuit breaker:** After N consecutive failures, stop calling and return graceful degraded response.
- **Model fallback chain:** Opus → Sonnet → Haiku → queue-for-retry.
- **Idempotent tool calls:** Key = (workflow_run_id + step_index + action_type) prevents double-execution.

### Observability Stack
- **89%** of production agent deployments have observability; **62%** have full distributed tracing.
- **LangSmith:** Native LangChain observability — threads, tools, sub-agents, memory as first-class concepts. LLM-as-judge evals integrated.
- **Langfuse:** Open-source, self-hostable. Traces, datasets, evals.
- **Arize Phoenix:** ML observability with LLM/agent tracing.
- **AgentOps:** Specialized agent monitoring with session replay.
- What to trace: every LLM call (input/output/tokens/latency), every tool call, every state transition, human intervention events.

---

## 8. Competitive Moats (Weakest → Strongest)

1. **Prompt engineering / system prompt** — Zero defensibility. Anyone can copy.
2. **UI/UX** — Low. Fast follower risk.
3. **API-based with fine-tuning** — Moderate. Fine-tuning data has some value.
4. **Proprietary data flywheel** — Strong. Self-reinforcing: more customers → more task data → better model → more customers.
5. **Deep workflow integrations** — Strong. Compliance certifications + API integrations to regulated systems took thousands of engineering hours.
6. **Network effects from user-generated data** — Strongest. Each new user improves the product for all users.

### Data Flywheel Mechanics
- Every task execution generates labeled ground truth: did the agent succeed? Where did it fail?
- This trains specialized models hyperscalers can never access (HIPAA-protected EHR data, attorney-client privileged documents, proprietary financial transaction logs).
- Harvey's moat: deep legal citation systems + tens of thousands of hours of proprietary legal datasets.

### Vertical Specialization Rules
- Vertical players: 3–5x higher retention rates vs. horizontal; premium pricing; lower competitive surface.
- Horizontal platforms: larger TAM but commoditization risk as foundation models improve.
- **Winner pattern:** Go vertical first, expand horizontally once you dominate the vertical. Never start horizontal.

---

## 9. Fundraising Landscape (2025–2026)

### Market Scale
- Full-year agentic AI funding: ~$1.5B (2024) → ~$2.9B (2025) → $3.3B/year pace (2026).
- Corporate AI agents market: $5.25B (2024) → projected $52.62B by 2030.

### Seed Stage
- Median pre-money: ~$17.9M; median round: ~$9.7M.
- "Agentic" label commands 40% valuation premium vs. "Generative AI Tools."
- Expectations: working prototype, clear vertical thesis, founding team with domain + AI engineering depth, early user signals.

### Series A
- Median pre-money: ~$84M; post-money: ~$105M.
- Expectations: $1–3M ARR or strong trajectory, defined GTM motion, measurable TSR, evidence of retention/expansion.

### Key Investor Theses
- **YC (W25/S25):** Vertical specialists building "digital coworkers"; infrastructure picks-and-shovels (~30% of S25).
- **a16z:** Vertical companies with proprietary data advantages; 50+ AI seed/early-stage deals in 2025.
- **Sequoia:** Category leaders in high-value professional verticals (legal, finance, healthcare).
- **Focus areas:** Legal, security, healthcare, procurement, finance, customer support, coding; memory, observability, evaluation, orchestration infrastructure.

---

## 10. Team Composition

### Founding Team (Pre-Seed)
1. **Technical co-founder / AI Engineer:** Ships LLM-powered features using existing model APIs. Integrates tools, builds agent loop, handles prompt engineering. Does NOT need ML research background — needs production engineering skills.
2. **Domain expert co-founder:** Knows the vertical cold (ex-lawyer, ex-nurse, etc.). Defines task taxonomies, labels ground truth, talks to customers.

### Seed Stage Hires (Priority Order)
1. AI Engineer #2 — Full-stack with LLM API experience
2. Product designer / PM — Human-in-the-loop UI determines trust and adoption
3. Solutions Engineer / Customer Success — Handles onboarding and integrations for design partners

### Growth Stage (Post-Series A)
4. ML Engineer — Joins when you have proprietary data and clear metrics. Trains/fine-tunes domain-specific models. ~23% higher base pay than AI engineers.
5. MLOps / AI Infrastructure Engineer — Production model serving, A/B testing, monitoring.
6. Security/Compliance Engineer — Required for regulated verticals (HIPAA, SOC2, legal).

### AI Engineer vs. ML Engineer

| Dimension | AI Engineer | ML Engineer |
|---|---|---|
| Primary work | Integrate APIs, build agent systems, deploy workflows | Train/fine-tune models, build data pipelines |
| When to hire | First — to ship fast | Later — when proprietary data + measurable lift matter |
| Compensation | Base | ~23% premium |

---

## 11. Top Companies and Approaches (2025–2026)

### Category Leaders
- **Harvey (Legal AI):** $11B valuation (Mar 2026), $190M ARR (Jan 2026). Deep legal citation integration + fine-tuned legal models + outcome-based pricing.
- **Sierra (Customer Service):** $10B+ valuation (Sep 2025), $350M Series C. Enterprise CS agents. Outcomes-based pricing. Heavy human-in-the-loop.
- **Cognition/Devin (Software Eng):** $10.2B valuation, $400M Series C. Autonomous software engineer.

### Infrastructure
- **LangChain:** LangGraph (orchestration) + LangSmith (observability) + LangMem (memory).
- **Temporal:** Durable execution for long-running agent workflows.
- **Mem0:** Managed memory, 80% token reduction.
- **Zep:** Temporal Knowledge Graph memory.

---

## 12. Common Failure Modes and Fixes

| Failure Mode | Symptom | Fix |
|---|---|---|
| Tool Misuse (~31% of failures) | Wrong argument types, semantically wrong parameters | Strict JSON Schema validation; log every tool call; test tools in isolation |
| Context Drift / Hallucination Cascades | Agent A hallucinates → Agent B reasons on it → plausible but wrong output | Verify at handoff boundaries; sanity-check agents; lean context windows |
| Goal Drift | Agent drifts from original objective, optimizes for proxy | Re-inject original goal at each major step; immutable `original_goal` in state |
| Infinite Loops | Same tool called repeatedly, no progress | Hard turn limits (max 20 LLM calls); loop detection; timeout with escalation |
| Prompt Injection | External content hijacks agent behavior | Never interpolate external content into system prompts; sanitize all tool outputs |
| Deploying Too Early | Works in demo, collapses on live data | Staging with real data; tracing must be on day 1 |
| Thin Wrapper Syndrome | Product is just a UI wrapping GPT-4; competitors copy in a weekend | Build proprietary data assets; focus on workflow depth |
| Wrong Pricing Model | Outcome-based before TSR verified → SLA violations | Start with usage-based; move to outcome-based only when TSR >90% consistently |
| Data Quality Failures (~43% of failures) | Agents operating on incomplete/stale data | Data validation at agent input, not just model training |
| Cost Overrun at Scale | LLM bills unsustainable at 10x volume | Benchmark cost per task at prototype; use smaller models for subtasks |

---

## Expert Mental Model

The agentic AI era follows a predictable arc: **demos → prototypes → production → moats**. Most startups are stuck between prototype and production. The breakouts share five traits:

1. **Vertical depth:** One domain, gone all the way in.
2. **Production-grade reliability:** Observability from day 1, idempotent operations, durable execution.
3. **Data flywheel activated:** Every task execution makes the product smarter.
4. **Pricing aligned with trust:** Outcome-based pricing gated behind proven TSR.
5. **Human-in-the-loop by design:** Compliance is a feature, not a limitation.

The framework wars are largely resolved: LangGraph for production control, CrewAI for multi-role collaboration, vendor SDKs for native deployments. The hard work — and the moat — is in domain data and workflow integrations that cannot be bought.
