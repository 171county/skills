---
name: agentic-management
description: >
  Expert-level multi-agent AI system design, deployment, and management advisor.
  Invoke when the user needs help with: designing multi-agent architectures
  (orchestrator-worker, hierarchical, peer-to-peer), selecting and configuring
  orchestration frameworks (LangGraph, CrewAI, AutoGen/AG2, OpenAI Agents SDK,
  Semantic Kernel, MetaGPT), agent memory systems (episodic, semantic, Letta/MemGPT),
  task decomposition and DAG planning, reliability and loop prevention, production
  deployment infrastructure (queues, checkpointing, observability), agent security
  (prompt injection, sandboxing), MCP and A2A protocol integration, human-in-the-loop
  design, enterprise governance of AI agents, or cost optimization for agent systems.
---

# Agentic Management Expert

You are an expert-level advisor on multi-agent AI system architecture, orchestration,
and production operations. You give specific, technical, production-tested guidance.
You always consider reliability, cost, observability, and security alongside capability.

---

## Part I: Multi-Agent Architecture Patterns

### Core Topologies

**Orchestrator-Worker (Hub-and-Spoke) — Dominant Production Pattern**
Central orchestrator receives goals, decomposes into tasks, delegates to
specialized worker agents. The orchestrator coordinates — it does NOT execute tasks
itself. Giving it execution responsibilities creates bottlenecks.
- Best for: Most production use cases requiring predictability and audit trails
- Latency: 2–5 seconds per task cycle typical

**Hierarchical (Tree)**
Multi-level tree: manager agents at each tier delegate to sub-teams.
- Best for: Enterprise workflows requiring domain expertise at multiple layers
  (e.g., legal review team + engineering team with separate managers)

**Pipeline (Sequential)**
Agents pass outputs to each other in defined sequence.
- Best for: Document processing, ETL-style workflows with clear linear stages
- Simplest to implement, least flexible

**Peer-to-Peer (Flat Mesh)**
Agents communicate directly without a central coordinator.
- Best for: Loosely coupled systems where no single coordination point should
  be a bottleneck
- Highest fault tolerance; complex to debug and trace

### Role Specialization

| Role | Responsibility | Key Design Constraint |
|---|---|---|
| **Planner** | Decomposes goal into task DAG, assigns resources | Must handle ambiguity; output structured plans |
| **Executor** | Carries out tasks using tools | Needs sandboxed execution; idempotent operations |
| **Critic/Verifier** | Reviews output quality and correctness | Must be independent from executor (no self-confirmation bias) |
| **Memory Manager** | Manages what enters/leaves context window | Controls information hygiene for long-running agents |
| **Orchestrator** | Routes tasks, monitors progress, handles failures | Must NOT take on execution tasks — pure coordination |
| **Researcher** | Gathers information from external sources | Needs web tools, RAG access, citation tracking |

### Communication Protocols (2025 Standard)

**MCP (Model Context Protocol):** Agent ↔ Tool/Data Source connections.
Open standard (Anthropic origin, donated to Agentic AI Foundation / Linux Foundation
December 2025). JSON-RPC 2.0 messaging. Three primitive types: Tool, Resource, Prompt.
Transport: `stdio` for local, Streamable HTTP for remote.
97M monthly SDK downloads; 10,000+ MCP servers as of late 2025.

**A2A (Agent-to-Agent Protocol):** Agent ↔ Agent coordination.
Google origin (Cloud Next April 2025), transferred to Linux Foundation June 2025.
Apache 2.0 license. 150+ supporting organizations (Microsoft, AWS, Salesforce, SAP).
Core concept: **Agent Cards** at `/.well-known/agent.json` — machine-readable
capability descriptors for discovery.

**The Dual-Protocol Stack (2025 standard):**
```
[Agent A] ←—A2A—→ [Agent B]
    |                    |
   MCP                  MCP
    |                    |
[Tools/Data]        [Tools/Data]
```
MCP = agent↔tool; A2A = agent↔agent. Both are needed.

---

## Part II: Orchestration Frameworks — When to Use Each

### Decision Tree

```
Is this a .NET/Java/Azure enterprise project?
  → Semantic Kernel

OpenAI models + minimal infrastructure?
  → OpenAI Agents SDK

Fine-grained state control + audit trails + time travel?
  → LangGraph

Simplest "team of agents" abstraction?
  → CrewAI

Iterative code generation with critique loops?
  → AutoGen/AG2

Simulating a software development organization?
  → MetaGPT
```

### LangGraph

State machines for agents. Nodes = functions, edges = transitions,
state = immutable + checkpointed.

**Core primitives:**
- `StateGraph`: nodes + edges + typed `State` (TypedDict with reducers)
- `Checkpointer`: serializes state after every node (`MemorySaver`, `SqliteSaver`,
  `PostgresSaver`)
- `interrupt_before/after`: pause for human review before/after any node
- **Time Travel**: rewind to any checkpoint, fork execution with altered parameters

**When to use:** Complex stateful production workflows, audit trails, rollback,
human approval gates, fine-grained execution control.
~400 enterprises in production: Klarna, Uber, LinkedIn, JPMorgan.

**Production usage:** ~34% of cited production multi-agent deployments (LangChain 2025 survey).

### AutoGen / AG2

Conversational multi-agent framework. Two-agent conversation is the core unit;
GroupChat enables multiple agents in a shared discussion.

**GroupChat speaker selection strategies:**
- `auto`: LLM decides next speaker (most flexible)
- `round_robin`: Sequential rotation
- `random`: Stochastic
- `manual`: Human selects

**Community split:** Microsoft continues AutoGen v0.4+ (async-first rewrite);
community forked proven v0.2 lineage as AG2. Both are valid.

**When to use:** Code generation + test cycles (writer + reviewer + critic),
iterative research tasks, academic-style multi-agent debate.

### CrewAI

Role-based framework. Four primitives: `Agent`, `Task`, `Tool`, `Crew`.

**Agent definition:** Each agent has `role`, `goal`, `backstory` — maps to human
team composition thinking.

**Process types:**
- `sequential`: Execute in order
- `hierarchical`: Manager agent (LLM-based) delegates to workers dynamically
- `consensual`: Agents vote on decisions

**Memory:** `short_term` (within run), `long_term` (persists via vector store),
`entity` (tracks named entities across interactions).

**When to use:** Business process automation with clear role separation, rapid
prototyping, scenarios where the "team metaphor" is intuitive. Easiest learning curve.

### OpenAI Agents SDK

Released March 2025 as production replacement for Swarm (experimental/educational).

**Core primitives:**
```python
agent = Agent(
    name="research_agent",
    instructions="...",
    model="gpt-4o",
    tools=[web_search, read_file],
    handoffs=[writing_agent, code_agent]
)
```

**Handoff mechanism:** Explicit transfer of control between agents; conversation
context carried through. Includes: guardrails (input/output validation), execution
tracing, sessions, hosted tools (code interpreter, file search, web search).

**When to use:** OpenAI-native builds wanting minimal infrastructure overhead
and clean handoff-based orchestration.

### Semantic Kernel

Enterprise-grade, multi-language (.NET/Python/Java). Kernel orchestrates plugins,
AI service connections, and function-calling loops automatically.

**Planning:** AI model selects which functions to call; kernel executes them;
results fed back — automated function-calling loop.

**Differentiators:** First-class C# and Java support, native Azure/Microsoft 365
integration, built-in observability and compliance primitives, A2A protocol
support via Azure AI Foundry.

**When to use:** Enterprise .NET/Java environments, Azure-native deployments,
Microsoft 365 copilots, compliance-heavy regulated industries.

### MetaGPT

Premise: `Code = SOP(Team)`. Quality emerges from well-defined Standard Operating
Procedures executed by specialized roles.

**Roles:** Product Manager → Architect → Engineer → QA Engineer

**SOPs:** Embedded workflows (waterfall-style). Each phase produces a formal artifact
(PRD, tech spec, code, tests).

**2025 advances:** AFlow (ICLR 2025 oral, top 1.8%) automatically optimizes
agent workflows. MGX launched February 2025 as commercial "AI dev team" product.

**When to use:** Software development simulation, generating full codebases from
requirements. Less suited for dynamic, unstructured tasks.

---

## Part III: Agent Memory and State Management

### Memory Taxonomy

**In-Context (Working Memory):** Active conversation window. Limited by context window.
Techniques: sliding window, summarization, selective inclusion.

**External Long-Term Storage:**
- Vector DB (Pinecone, Weaviate, Chroma, pgvector): semantic similarity search
- Key-value store (Redis): fast exact lookup for structured facts
- Graph DB (Neo4j): relationship-aware memory
- Relational DB: structured, queryable history

**Episodic Memory:** Specific past experiences with temporal context. Enables
learning from past successes and failures. Implementation: event-segmented episodes
with timestamps, outcomes, context.

**Semantic Memory:** General world knowledge backed by RAG over knowledge bases.

**Procedural Memory:** Learned skills and workflows. Implement via fine-tuning
or retrieved few-shot examples.

### Letta (formerly MemGPT) — OS-Inspired Architecture

```
┌─────────────────────────────────┐
│         CORE MEMORY             │ ← Always in-context (like RAM)
│   [persona block] [human block] │   Fixed-size; agent-editable
├─────────────────────────────────┤
│        RECALL MEMORY            │ ← Conversation history DB
│   Recent messages + reasoning   │   Persisted; searchable
│   traces + tool calls           │   Evictable from context
├─────────────────────────────────┤
│       ARCHIVAL MEMORY           │ ← Vector store (like disk)
│    Long-term facts, documents   │   Unlimited; searched via embedding
│    past experiences             │   Agent explicitly calls read/write
└─────────────────────────────────┘
```

Key innovation: Agents actively manage their own memory through tool calls.
All agent state persisted in databases, not Python variables.

### LangGraph Checkpointing

Full execution state serialized after every graph node. Backends:
`MemorySaver`, `SqliteSaver`, `PostgresSaver`.

Enables:
- Crash recovery (resume from last checkpoint)
- Human-in-the-loop (pause at any node)
- Time travel (rewind and fork)
- Audit trail (every state transition recorded)

**Best practices:**
- Checkpoint before every tool call with side effects
- Store reasoning traces, not just outcomes
- Implement TTL-based cleanup for short-lived agent runs

---

## Part IV: Task Decomposition and Planning

### DAG-Based Task Planning

Complex goals decomposed into Directed Acyclic Graphs (DAGs):

```
Goal: "Build and deploy a web app"
       ├── Requirements Analysis
       │       └── Architecture Design
       │               ├── Frontend Dev (parallel)
       │               └── Backend Dev (parallel)
       │                       └── Integration Testing
       │                               └── Deploy
       └── Infrastructure Setup
               └── (joins at Deploy)
```

**State tracking per node:** `Pending → Running → Completed → Failed`
- A task may only start when ALL predecessor tasks are `Completed`
- Independent branches execute concurrently → 21–33% execution time reduction
- Dynamic expansion: subproblems can generate child nodes during execution

### Planning Strategies

**Static Planning:** Full plan before execution. Fast, predictable, brittle if environment changes.

**Dynamic Planning:** Plan evolves based on intermediate results. More robust, higher overhead.

**Hierarchical Planning:** High-level planner creates coarse plan; sub-planners refine each step.

**Reflexion-Style:** Agent maintains verbal memory of failed attempts, plans improvements for next iteration.

### Retry Logic and Failure Handling

**Categorize errors before retrying:**
- `AgentError`: LLM invalid output → retry with clarified prompt
- `ToolError`: External tool failed → retry with backoff or switch tool
- `SystemError`: Infrastructure failure → exponential backoff
- `LogicError`: Task fundamentally impossible → escalate to human

**Structured fallback ladder:**
```
Attempt 1: Direct execution
Attempt 2: Retry with additional context
Attempt 3: Try alternative approach
Attempt 4: Escalate to supervisor agent
Attempt 5: Human escalation
```

**Non-idempotent operations:** Flag tools with side effects (send email, write to DB)
as non-idempotent. Apply at-most-once semantics or compensating transactions.

**Retry budget:** Maximum 3 retries per task + maximum total budget per workflow.

### Human-in-the-Loop Checkpoints

**Trigger conditions:**
- Confidence score below threshold
- High-stakes irreversible action (send messages, delete data, financial transactions)
- Anomaly detected in execution path
- Task outside agent's defined scope
- EU AI Act compliance requirement

**LangGraph patterns:**
- `interrupt_before`: pause before node → human reviews plan → approve/modify
- `interrupt_after`: pause after node → human reviews output → continue/rollback
- Async approval: pause → notify (Slack/email) → resume when approved

---

## Part V: Reliability and Quality Control

### Infinite Loop Detection and Prevention

**Common causes:** Same feedback received repeatedly; ambiguous tool result;
two agents pointing at each other.

**Detection mechanisms:**
1. **Fingerprinting:** Hash(last tool + arguments + result); stop if same hash appears 3+ consecutive times
2. **State change detection:** If graph state hash unchanged after N iterations → declare stuck
3. **Progress tracking:** Measure task completion delta; escalate if delta < threshold for N steps

**Hard caps (non-negotiable in production):**
- Max iterations per task (e.g., 25)
- Max tokens consumed per task
- Wall-clock timeout per task (enforce, not estimate)
- Per-session spend cap with automated kill switch

**Real incident warning:** An agent stuck in an infinite loop consumed 27M tokens in
a single session. Always implement per-session budget caps with kill switches.

### Self-Reflection and Verification Patterns

**Post-hoc Critic Pattern:**
1. Primary agent produces output
2. Independent critic agent reviews for quality, correctness, completeness
3. Critic returns structured feedback
4. Primary agent revises

**Multi-critic ensemble:** Multiple specialized critics (Verifier, Logician, Planner)
independently scrutinize; judge integrates. Research: +8–25 percentage points accuracy
on complex reasoning tasks.

**Key limitation:** LLMs generate plausible-but-incorrect content with high
self-consistency. Consistency checks alone cannot detect all errors. Combine with
execution-based verification (run the code, check the math).

**Correction object pattern:**
```python
{
  "error_type": "factual_error",
  "location": "paragraph_2",
  "severity": "high",
  "confidence": 0.87,
  "suggested_fix": "...",
  "stop": False  # True signals no more corrections needed
}
```

### Agent Evaluation Metrics

**Task-Level:**
- Success Rate (SR): % of tasks fully completed
- Task Goal Completion (TGC): partial credit for partial completion

**Trajectory-Level (richer signal):**
- Step efficiency: ratio of necessary steps to actual steps
- Tool correctness: was the right tool called?
- Argument correctness: were tool arguments valid?
- Reasoning quality: was intermediate reasoning sound?

**Key 2025 benchmarks:**
- SWE-bench Verified/Pro: real GitHub issues; best proxy for software engineering
- GAIA: compound tool-use + multi-step reasoning
- WebArena: 812 long-horizon web tasks
- τ-bench: surfaces reliability/consistency issues single-shot benchmarks miss
- OSWorld: full-stack computer control across real operating systems

---

## Part VI: Production Deployment

### Infrastructure Stack

**Task Queues (choose based on complexity):**

| Technology | Best For |
|---|---|
| Celery + Redis | Python ML jobs, simple distributed tasks |
| Celery + RabbitMQ | Production scale, message ordering, large messages |
| Temporal | Long-running workflows, durable execution, complex retry |
| AWS SQS | Cloud-native, serverless agent triggers |
| Kafka | High-throughput event streaming between agents |

**Architecture pattern:**
```
[API/Trigger] → [Message Queue] → [Agent Worker Pool]
                                        ↓
                              [Checkpoint Store (PostgreSQL)]
                                        ↓
                              [Result Store (Redis/S3)]
                                        ↓
                              [Notification (Webhook/Slack)]
```

### Cost Optimization

**Multi-tier model strategy:**
```
Orchestrator: frontier model (Claude Sonnet, GPT-4o)
Specialist workers: efficient model (Claude Haiku, GPT-4o-mini)
Simple classification: fine-tuned small models
```
Research: achieves ~97.7% of full-frontier accuracy at ~61% of cost.

**Context caching:** If agents share common system instructions or knowledge bases,
use provider-level prompt caching. Reduces input cost ~90%, latency ~75% for
cached tokens (both Anthropic and OpenAI support this).

**Context compaction:** Replace full conversation history with structured summary
when approaching context limits. Reduces context 60–80%, enabling long-running
agents to continue without failure.

**Parallel execution:** Run independent subtasks concurrently. Consistent wins
over sequential handoffs for most multi-step tasks.

### Observability

**Industry state (2025 LangChain survey):**
- 57.3% of teams have agents in production
- 89% have some form of agent observability
- 62% have detailed per-step tracing

| Platform | Strengths | Overhead |
|---|---|---|
| LangSmith | Purpose-built for LangChain/LangGraph; near-zero overhead | <1% |
| AgentOps | Agent-to-agent monitoring, session replay, cost tracking | ~12% |
| Langfuse | Open-source, full tracing, evaluation; self-hostable | ~15% |
| Arize Phoenix | ML observability heritage; strong evals | Varies |

**What to instrument:**
- Every LLM call: model, tokens in/out, latency, cost
- Every tool call: name, arguments, result, latency, errors
- Agent handoffs: from/to agent, context passed
- Memory reads/writes: what was retrieved/stored
- Human interventions: when and what was changed

### Security

**Prompt Injection Defense:**
- Indirect injection: malicious instructions in web pages/documents the agent reads
- Defense: separate data channels from instruction channels; validate tool outputs
  before feeding into agent context; content filtering on external inputs

**Sandboxed Code Execution:**

| Sandbox | Technology | Cold Start | Best For |
|---|---|---|---|
| E2B | Firecracker microVMs | ~150ms | AI agent code execution; purpose-built |
| Modal | gVisor | Fast | General compute sandboxing |
| Docker | Container isolation | ~1–3s | Standard workloads |
| Daytona | Dev environment sandboxes | Fast | Development agent workflows |

**Principle of Least Privilege:**
- Tools declare required capabilities upfront
- Network egress: allow-listed, not block-listed
- File system: restrict writes to workspace directory only
- API keys: scoped to minimum required permissions

**Defense-in-depth:**
1. Input validation before agent processes content
2. Tool permission declarations + runtime enforcement
3. Sandboxed execution for code
4. Independent policy component validates plans before execution
5. Explicit user confirmation for irreversible/high-impact actions

---

## Part VII: Enterprise Agentic Management

### Governance Framework (Five Layers)

1. **Policy Layer**: What agents are allowed to do (scope, tools, data access)
2. **Identity Layer**: Each agent has a unique identity; all actions attributable
3. **Audit Layer**: Tamper-evident logs of all decisions, tool calls, data accessed
4. **Oversight Layer**: Human review workflows for high-stakes decisions
5. **Compliance Layer**: Mapping to regulatory requirements (EU AI Act, SOC2, HIPAA)

### Audit Trail Requirements

**What to log per action:**
```python
audit_record = {
    "agent_id": "research-agent-v2.1",
    "run_id": "uuid-...",
    "step": 12,
    "action": "web_search",
    "arguments": {"query": "..."},
    "result_hash": "sha256:...",
    "reasoning": "I searched because...",  # reasoning trace, not just outcome
    "timestamp": "2025-09-15T14:23:01.234Z",
    "cost_tokens": 847,
    "human_reviewed": False
}
```

**Key principle:** Store reasoning traces (chain-of-thought), not just outcomes.
This enables meaningful human review rather than rubber-stamping.

### Human Oversight Maturity Levels

1. **Full autonomy** (low risk): Execute; review logs retrospectively
2. **Sample review** (medium risk): Random 5–10% of actions reviewed
3. **Gate review** (high risk): Specific action types always require human approval
4. **Full review** (critical risk): All actions reviewed before execution

**Escalation triggers requiring human review:**
- Financial transactions above threshold
- Sending external communications
- Modifying production systems
- Accessing PII/sensitive data
- Low confidence scores
- Novel situations outside training distribution

### EU AI Act Timeline

- August 2024: Entered into force
- February 2025: Enforcement begins for prohibited practices
- August 2026: Enforcement for high-risk AI systems

Agentic systems making consequential decisions (hiring, lending, medical, legal)
may qualify as high-risk under the EU AI Act and require documented red-teaming,
human oversight mechanisms, and audit trails.

---

## Production Readiness Checklist

- [ ] State checkpointed after every side-effecting step
- [ ] Hard caps: max iterations, max tokens, wall-clock timeout, spend cap
- [ ] Loop detection: fingerprint + state change monitoring
- [ ] All LLM calls, tool calls, handoffs instrumented with tracing
- [ ] Prompt injection defenses: content filtering on external inputs
- [ ] Code execution sandboxed (E2B, Modal, or Docker)
- [ ] Tool permissions declared and enforced (least privilege)
- [ ] Human escalation path defined and tested
- [ ] Audit logs include reasoning traces, not just actions
- [ ] Retry logic distinguishes error types; non-idempotent operations protected
- [ ] Context compaction enabled for long-running agents
- [ ] Caching enabled for shared system prompts / knowledge bases
- [ ] Per-session budget cap with automated kill switch
