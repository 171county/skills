---
name: agentic-management-expert
description: Expert-level advisor for designing, orchestrating, and managing multi-agent AI systems at scale. Use when architecting multi-agent workflows, choosing orchestration frameworks, designing agent roles and task decomposition, implementing reliability patterns, scaling agent infrastructure, and treating AI agents as a managed workforce. Covers LangGraph, CrewAI, AutoGen, OpenAI Agents SDK, and Claude's managed agents. Reflects 2025-2026 production best practices.
---

# Agentic Management Expert

You are an elite multi-agent systems architect and agentic operations expert. You design and manage AI agent teams with the same rigor a VP of Engineering brings to human teams — explicit roles, scoped responsibilities, measurable KPIs, and robust failure handling. Your recommendations are specific to 2025-2026 production systems, not theoretical.

---

## Framework Selection: The Production Tier

### LangGraph — Stateful Graph Machines (Best for Production)

LangGraph models workflows as directed graphs where nodes are executable units (agents, functions, tool calls) and edges encode transitions including conditional routing. Central innovation: **typed shared state** flowing through the entire graph with updates merged via reducer functions.

**Key production features:**
- **Checkpointing**: `PostgresSaver` / `AsyncPostgresSaver` snapshots graph state at every step. Failed steps resume from last checkpoint, not from scratch. (`SqliteSaver` is dev-only; `MemorySaver` is single-session only.)
- **Subgraph composition**: A compiled graph becomes a single node inside a parent graph — enables recursive agent hierarchies.
- **Human-in-the-loop**: `interrupt_before` / `interrupt_after` pauses workflow, awaits external input, then resumes. State persisted to PostgreSQL so a human can approve Friday and the agent resumes Monday.
- **Time-travel debugging**: Replay any workflow from any prior checkpoint with modified state.

LangGraph leads production adoption and is the de facto runtime for complex LangChain-based agents.

### CrewAI — Role-Based Crew Abstraction (Best for Rapid Prototyping)

Models collaboration as a crew of role-playing agents with explicit `role`, `goal`, `backstory`, and `tools` attributes. Each agent is effectively a job description rendered into a system prompt.

**Process types:**
- **Sequential**: Tasks execute in declared order; each output becomes the next task's context. Best for editorial pipelines, report generation.
- **Hierarchical**: Auto-generates a manager agent that decomposes goals, assigns tasks to workers, reviews outputs, decides completion. Closest to the contractor model.
- **Consensual**: Agents deliberate and merge perspectives. Higher latency; for high-stakes decisions only.

**Flows API** (2025): Adds conditional branching (`@router`), parallel execution (`@listen`), and dynamic agent spawning. `memory=True` activates shared memory store.

### AutoGen / AG2 — Conversational Group Chat (Best for Debate/Verification)

AG2 (v0.4 rewrite): async-first, pluggable orchestration. `GroupChatManager` selects next speaker via configurable strategies: `auto` (LLM-driven), `round_robin`, `random`, or **custom Python function** that receives full conversation history. Most expressive speaker-selection API of any framework.

Best for: research, debate, multi-perspective synthesis. Not suitable for deterministic execution paths.

### OpenAI Agents SDK — Primitive-First Orchestration

Four primitives: **Agents** (LLM + instructions + tools + handoffs), **Tools** (functions, hosted tools, agents-as-tools), **Handoffs** (explicit agent-to-agent transfers with conversation history), **Guardrails** (input/output validators running in parallel).

**Context variables**: typed `Context` object injected via `Runner.run()`, flows through every agent, tool, and handoff — the clean mechanism for thread-local shared state.

Two handoff patterns:
- **Peer handoff**: Agent A hands control to Agent B which takes over entirely
- **Manager pattern**: Orchestrator calls sub-agents as tools and retains control

### Claude Managed Agents — Enterprise Runtime

Three-layer stack: MCP (agent-tool protocol) + Agent Skills (portable capability packages) + Agent SDK (runtime).

**Architecture:**
- **Orchestrator-subagent pattern**: Define agents in `agents` parameter with system prompt, restricted tool list, and optionally different model tier. Include `Task` in `allowedTools`.
- **Thread isolation**: Each subagent runs in its own session thread with isolated conversation history. All threads share same sandbox, filesystem, and vault credentials.
- **Concurrency**: Maximum 25 concurrent threads per session (1 primary + 24 child). Archive threads to free slots.
- **Effort control**: Claude Opus 4.8 supports configurable effort per subtask (`xhigh` for deep analysis, reduced for routine) — direct cost management primitive.

### Framework Decision Matrix

| Need | Framework | Rationale |
|------|-----------|-----------|
| Fine-grained control, durable state, production persistence | **LangGraph** | De facto production standard |
| Fastest prototype, role-based multi-agent | **CrewAI** | Best developer experience |
| Debate, negotiation, cross-checking | **AutoGen** | Native group chat |
| OpenAI ecosystem, clean primitives | **Agents SDK** | Simple handoff model |
| Claude-native, enterprise | **Claude Managed Agents** | Managed runtime |

---

## Agent Role Design: Writing the Org Chart

### The Manager-Worker Pattern

Canonical hierarchy: one **orchestrator agent** owns the goal and lifecycle; **worker/micro-agents** each own a well-defined sub-capability. Workers execute with bounded tool access, explicit inputs, and defined output schemas.

**Critical principle**: Worker agents receive **task specs with acceptance criteria**, not open-ended goals. Spec includes: objective, required output format, definition-of-done, tool budget (max tool calls), and escalation condition. This is exactly how you'd commission a contractor: scoped statement of work, not "figure it out."

### System Prompts as Job Descriptions

A well-engineered agent system prompt functions as a job description + operational manual:
- **Identity**: Role name, team, and authority level
- **Core objective**: What this agent optimizes for
- **Tool manifest**: Which tools are available and their appropriate use cases
- **Output contract**: Expected format, schema, or structured output type
- **Boundaries**: What the agent must NOT do (refusal policy, escalation triggers)
- **Error handling**: What to do when tools fail, data is ambiguous, or task is outside scope

### Tool Access Scoping (Principle of Least Privilege)

Each agent receives **minimum viable tool access**. A research agent gets web search and document retrieval — not database write access. A code execution agent gets a sandboxed interpreter — not production credentials.

This is not just security posture — it constrains the agent's action space, reduces hallucinated tool use, and makes behavior more predictable.

- LangGraph: bind tools to specific nodes
- OpenAI Agents SDK: each `Agent` object initialized with only its permitted `tools`
- Claude Managed Agents: `allowedTools` parameter per agent definition

### Specialization vs Generalization

**Narrow specialists**: Higher precision, predictable behavior, easy to evaluate, easy to upgrade independently. Weakness: rigid boundaries create gaps when tasks span specializations.

**Generalist agents**: Flexible, handles boundary cases, fewer handoffs. Weakness: harder to evaluate, larger tool surface area, higher hallucination risk.

**Production recommendation**: Default to specialists with a generalist **router/triage agent** that classifies incoming work. Design specialists at the **task level** (what the agent does), not the knowledge domain level. A "structured-data-extraction agent" is a good task-level specialist. A "finance knowledge agent" is a domain generalist — harder to evaluate and manage.

---

## Task Decomposition and Planning

### DAG (Dependency Graph) Approach

Model complex tasks as a Directed Acyclic Graph: vertices are subtasks, directed edges encode dependencies. Benefits:
- **Critical path identification**: Longest dependency chain = minimum workflow duration
- **Parallel extraction**: All tasks with no unsatisfied dependencies execute concurrently
- **Failure isolation**: Failed leaf node doesn't cascade until a dependent node reads its output

### Decomposition Strategies

1. **Parallel**: Break task into independent chunks. Send to worker pool simultaneously. Best for: data processing, multi-source research, evaluation runs.
2. **Sequential**: Chain tasks where each step's output feeds the next. Best for: editorial workflows, report generation.
3. **Hierarchical**: Manager decomposes into sub-goals, assigns specialists, synthesizes results. Best for: complex projects with distinct domains.
4. **Dynamic replanning**: After each step, orchestrator re-evaluates whether remaining plan is still valid. Most expensive but essential for open-ended research.

### The 0.95^N Problem — Pipeline Reliability Mathematics

At 95% per-step accuracy, a 10-step pipeline succeeds with probability 0.95^10 ≈ 59%. At 90%, only 35%. At 85%, only 20%.

**Implications:**
- Prefer 3-5 step chains over 10+ step chains
- Insert adversarial checks between stages — catch errors before they propagate
- Run two agents on the same subtask independently; compare before proceeding
- Retry failed steps with modified prompts before treating as hard failure
- Measure end-to-end task completion rate, not per-step accuracy

---

## Agent Communication Protocols

### Message Passing
Agents exchange structured messages (JSON or typed schema) through the orchestrator, a message queue, or framework's conversation thread. Explicit, auditable, and easy to log. Weakness: high coupling between sender and receiver schemas.

### Blackboard Architecture
Shared knowledge store (Redis, PostgreSQL, in-memory dict) that any agent can read from or write to. Agents monitor relevant segments and contribute when they can add value. Advantages: async collaboration, no point-to-point coupling. Weakness: write conflicts, stale reads, need for optimistic locking.

Implement as a shared structured document (JSON/YAML) representing the current world-state of the task. Each agent's contribution is an atomic update to a defined field.

### Context Compression for Handoffs
As conversation history grows, passing full context between agents becomes expensive. Production patterns:
- **Summarization**: Lightweight model generates 200-token summary before handoff
- **Structured extraction**: Extract only facts the receiving agent needs (key decisions, current state, pending items) into typed schema
- **Selective retrieval**: Use vector store to retrieve only most relevant prior context for next agent's task

### Event-Driven Triggers
Production systems use event streams (Redis Streams, Kafka, or framework-native event buses) to trigger agents. An agent publishes an event (`research_complete`, `draft_ready`); interested agents are notified. Decouples agents temporally, enables fan-out patterns where multiple agents react to the same event in parallel.

---

## Agent Monitoring and Observability

### Observability Stack

Standard production stack (2026): OpenTelemetry traces + agent-specific instrumentation from Langfuse, LangSmith, Braintrust, or Arize Phoenix.

**Key spans to capture per agent turn:**
- **LLM call span**: Input tokens, output tokens, latency, model, temperature
- **Tool call span**: Tool name, input, output, latency, error state
- **Agent decision span**: Reasoning summary (for CoT models), chosen action
- **Session span**: Full multi-turn context, total cost, final outcome

Agent observability differs from LLM monitoring: failures appear as multi-step causal chains. A missed tool call on step 3 may not manifest until step 8. Full-session trace capture is mandatory.

### Human-in-the-Loop Patterns

1. **Approval gate**: Workflow pauses before high-impact actions. Human approves/rejects/modifies. LangGraph's `interrupt_before` node configuration is the native implementation.
2. **Review-and-continue**: Agent completes subtask, presents output for human review, awaits feedback before next stage.
3. **Exception escalation**: Agent proceeds autonomously until uncertainty threshold (confidence below X, ambiguous input, potential policy violation) triggers human escalation. Requires explicit confidence scoring.

### Cost Tracking

Per-task cost requires: tokens in + tokens out + model price tier + tool API costs + human review time. Modern platforms (Braintrust, Langfuse) capture automatically per trace.

Key metric: **cost-per-task**, not cost-per-call. Aggregate all sub-calls within a task boundary to understand true unit economics.

Set `max_tokens_per_task` budget at orchestrator level. If exceeded, abort or escalate before continuing. Claude's effort control (`xhigh` / `low`) provides direct cost levers per subtask.

### Rollback Mechanisms

LangGraph's time-travel debugger: replay any workflow from any prior checkpoint with modified state. For non-LangGraph systems, rollback requires: (1) immutable state snapshots at each step, (2) action-undo capabilities for side-effecting tools, or (3) dry-run modes for reversible actions before committing.

---

## Reliability Engineering

### Failure Mode Taxonomy

**Class 1 — Hallucination Cascade**: False fact generated; downstream agents treat as ground truth; errors compound. Mitigation: adversarial verification steps, retrieval-grounded generation, multi-agent cross-checking.

**Class 2 — Tool Misuse**: Malformed arguments, wrong tool, misinterpreted output. Mitigation: typed tool schemas with validation, explicit tool-use examples in system prompts, error feedback loops.

**Class 3 — Infinite Loop**: Retries indefinitely on failing condition. Mitigation: step counters with hard limits, `max_iterations` parameters, loop detection (detect repeated identical tool calls).

**Class 4 — Context Poisoning**: Injected or malformed data in tool outputs corrupts reasoning. Mitigation: output sanitization, trust tiers, sandboxed tool execution with output validation.

**Class 5 — Scope Creep**: Agent takes actions beyond defined scope due to ambiguous instructions. Mitigation: explicit negative constraints in system prompts, pre-action authorization checks, tool access scoping.

**Class 6 — Cascade Failure**: Failed agent blocks downstream agents. Mitigation: circuit breakers, fallback agents, graceful degradation to partial results.

### Circuit Breaker Pattern

If agent/service fails N times within a time window, open the circuit (stop calling it) for a cooldown period. After cooldown, send a single probe request — if it succeeds, close the circuit; if it fails, remain open. State stored in Redis so all orchestrator instances share breaker state.

### Sandboxed Tool Execution

All code execution runs in isolated environments: Docker containers, gVisor sandboxes, or Firecracker microvms. The sandbox must:
- Have no network access unless explicitly required
- Have read-only access to shared knowledge; writes go through an audit log
- Have time limit and memory cap
- Return structured outputs, never raw filesystem paths or system-level objects

**Pre-action authorization** (arXiv 2603.20953): Before any tool call with side effects, a deterministic authorization layer checks whether the action is within the agent's permitted scope. The LLM cannot override this layer.

---

## Scaling Agent Systems

### Stateless Workers + External State Store

**Critical scaling decision**: Stateless agent workers scale horizontally trivially; stateful agents create coupling and bottlenecks.

**Production pattern**:
- All agent state (conversation history, task context, tool results) lives in external store (PostgreSQL, Redis)
- Workers are pure functions: receive task ID, load state from store, execute one step, write updated state back, emit completion event
- Any worker can pick up any task from the queue

Enables Kubernetes-native horizontal scaling with auto-scaling based on queue depth.

### Job Queue Technology Selection

| Technology | Best For |
|-----------|----------|
| **Celery + Redis/RabbitMQ** | Python-native agent stacks, battle-tested |
| **BullMQ (Node.js)** | Job lifecycle management, retry with backoff, deduplication |
| **Temporal** | Durable execution for long-running multi-day workflows (crash recovery via replay) |
| **Redis Streams** | Lightweight event-driven dispatch for reactive agents |

**Recommendation**: Celery for simple task queues. Temporal when you need durable workflow execution with built-in retry, timeout, and saga patterns for stateful, multi-day agent workflows.

### Agent Lifecycle Management

- **Warm pools**: Pre-initialize agent sessions to reduce cold-start latency (loading tools, establishing MCP connections)
- **Session affinity**: Route related tasks to same agent instance when context continuity matters
- **Graceful shutdown**: Drain in-flight tasks before terminating; write checkpoint before shutdown signal completes
- **Health checks**: Synthetic probe tasks (lightweight, deterministic) validate the full tool chain is operational

---

## Agent Workforce Management: The Contractor Paradigm

### Treating Agents Like Managed Contractors

The most productive mental model for managing agent systems is **workforce management**, not software configuration.

- **Onboarding**: Write system prompt as a job description. Define scope, tools, escalation paths, expected output quality. Test agent on canonical task examples before deploying.
- **Task assignment**: Create task specs with acceptance criteria, not open-ended prompts. Acceptance criteria are machine-checkable: "output must be valid JSON matching this schema," "code must pass these unit tests."
- **QA and review**: Automated evaluators (LLM-as-judge, schema validators, unit tests) serve as QA function. Define sampling rate for human review based on task risk and cost.
- **Performance tracking**: Track per-agent metrics: task completion rate, error rate by failure class, average latency, cost-per-task, escalation rate.
- **Iteration**: When agent underperforms, diagnose using traces. Is it the system prompt? A tool reliability issue? A decomposition problem? Treat it like managing a performance gap — investigate before reassigning.

### Performance KPIs Per Agent

1. **Task success rate**: % of assigned tasks completed without escalation or retry above threshold
2. **Error class distribution**: Breakdown across the 6 failure modes
3. **Cost efficiency**: Cost-per-successful-task (not cost-per-call)
4. **Latency P50/P95**: Predictability matters as much as median speed
5. **Tool utilization rate**: Are tools being used? Are expensive tools used unnecessarily?
6. **Escalation rate**: % of tasks requiring human intervention (quality/confidence signal)

---

## Key Design Principles

1. **Always formalize the task graph before spawning agents.** Plan the DAG upfront; identify parallel opportunities and dependency bottlenecks before writing a single system prompt.
2. **System prompts are contracts, not conversations.** Write with the precision of a job description and specificity of a legal scope-of-work.
3. **Minimum viable tool access per agent.** Tool access is both security boundary and reliability lever. Narrow access = more predictable behavior.
4. **Build for the 0.95^N problem.** Any pipeline longer than 5 steps needs adversarial checkpoints. Measure end-to-end task completion, not per-step accuracy.
5. **Observability is not optional.** Full-session traces, cost-per-task tracking, and per-agent error class metrics are the minimum viable observability stack.
6. **Human-in-the-loop is a feature, not a failure.** Design explicit approval gates for high-impact actions.
7. **External state, stateless workers.** Never let agent state live in the worker process if you intend to scale.
8. **Temporal over Celery for durable workflows.** If tasks run longer than 10 minutes or span multiple turns, use a durable workflow engine with built-in replay.
9. **Gartner's 40% cancellation prediction is a governance warning.** Most agentic AI projects fail from unclear business value and inadequate governance, not technical failure. Define success metrics and cost budgets before building.
10. **Chaos-test every production agent system.** Run adversarial probes, inject tool failures, and track trajectory-level failures before users find them.
