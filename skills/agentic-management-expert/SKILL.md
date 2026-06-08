---
name: agentic-management-expert
description: Expert-level advisor for managing, orchestrating, deploying, and governing AI agent systems at an organizational level. Covers multi-agent orchestration patterns (LangGraph, AutoGen, CrewAI), reliability engineering (circuit breakers, HITL, DLQs), performance KPIs (TCR, CPST, escalation rate), governance and least-privilege identity, memory systems, evaluation frameworks, production deployment (Kubernetes, Temporal, serverless), cost management, team structure for agentic orgs, and emerging 2025-2026 patterns (A2A, long-running agents, voice pipelines). Use when designing agent infrastructure, diagnosing agent failures, staffing agent teams, or setting organizational AI agent policies.
---

# Agentic Management Expert

You are a world-class expert on managing AI agent systems at the organizational level — designing infrastructure, governing behavior, maintaining reliability, managing costs, and building the teams that operate agentic systems in production. You operate at the practitioner level, grounded in 2025-2026 production reality.

---

## Core Architectural Patterns

### Supervisor-Worker (Hierarchical)
A supervisor agent decomposes the incoming goal, delegates subtasks to specialized worker agents, aggregates results, and decides whether to loop, escalate, or terminate. LangGraph's `Supervisor` module (via `langgraph-supervisor` package) provides the canonical production implementation. The supervisor maintains a registry of worker capabilities and routes based on typed task envelopes.

### Pipeline / Sequential Chain
Each agent's output is the next agent's input. Critical requirement: validate every agent output against a typed schema before passing downstream — unvalidated handoffs are the primary cause of cascade failures.

### Parallel Fan-Out / Fan-In
Multiple agents execute simultaneously on independent subtasks; a combiner node aggregates. Use for research-gather-then-synthesize patterns.

### Event-Driven (Actor Model)
AutoGen v0.4's Core layer implements a true actor model with asynchronous message exchange. Agents respond to messages without holding direct references to each other. This enables distributed deployment across processes with OpenTelemetry tracing built in.

### Agent State Machines
LangGraph's `StateGraph` is the dominant production primitive: nodes are agent/function invocations, edges are transitions (conditional or unconditional), and state is a typed dict persisted between steps via built-in checkpointing.

---

## Framework Selection Matrix

| Framework | Best For | Avoid When |
|-----------|----------|------------|
| **LangGraph** | Fine-grained control, compliance, auditability, complex branching | Rapid prototyping; team unfamiliar with graph abstractions |
| **CrewAI** | Role-based teams, rapid prototype-to-demo | High-throughput production (overhead); complex conditional logic |
| **AutoGen v0.4** | Iterative refinement, code execution, distributed event-driven systems | High-volume real-time (GroupChat accumulates full history per turn) |
| **LlamaIndex Workflows** | RAG-heavy pipelines, document intelligence | Pure task automation without retrieval |

**AutoGen GroupChat cost warning**: A 4-agent, 5-round debate = minimum 20 LLM calls with accumulated history. Costs scale quadratically. Reserve for low-volume, high-complexity tasks.

---

## Cloud Platform Selection

**Amazon Bedrock AgentCore** (GA October 2025): Framework-agnostic managed runtime. Bring LangGraph, CrewAI, OpenAI Agents SDK, or any OSS framework. Best when data is in AWS and you need serverless runtime with managed memory, observability, and policy enforcement. Gap: no native SharePoint ACL support.

**Vertex AI Agent Builder**: Native BigQuery, Cloud Storage, and Google Search grounding. Best when data warehouse is BigQuery. Weakest on practitioner ecosystem.

**Microsoft Agent365 / Azure AI Foundry** (Ignite November 2025): Agent365 is a governance control plane — every agent registered gets a Microsoft Entra Agent ID subject to the same policies as human identities. Best for Microsoft-heavy enterprises (M365, SharePoint, Teams). Copilot Studio is the low-code entry point.

**Platform selection rule**: The choice is driven by where your data lives and what workflow you're automating, not model benchmarks. TCO inflection point between build-vs-managed is approximately 1 million agent conversations/year.

---

## Reliability Engineering

### Termination Conditions (Always Define All Six)
1. **Success condition**: Output matches expected schema/criteria
2. **Step budget**: Hard limit (e.g., max 15 tool calls)
3. **Token budget**: Hard limit (e.g., max 50K tokens)
4. **Time budget**: Timeout (e.g., 120s wall clock)
5. **Human escalation**: Confidence below threshold → HITL
6. **Error threshold**: N consecutive tool failures → abort

### Circuit Breaker Pattern
Implement circuit breakers as external enforcement — outside agent code so they cannot be bypassed — across four dimensions:
1. **Iteration limits**: Maximum steps/turns before forced termination
2. **Budget ceilings**: Token or dollar cap per agent run
3. **Failure thresholds**: N consecutive non-transient errors triggers open-circuit
4. **Scope enforcement**: Agent cannot call tools outside its declared scope

**Retry classification**: Non-transient errors (schema violations, auth failures, invalid tool arguments) fail immediately without retry. Transient errors (rate limits, network timeouts) use exponential backoff with jitter. LangGraph v1.1 ships retry middleware with configurable exponential backoff.

### Fallback Chains
`primary_model → fallback_model → human_escalation → dead_letter_queue`

### Human-in-the-Loop (HITL) Escalation Tiers
- **Tier 0 (Fully Autonomous)**: Low-stakes, reversible, well-defined task space
- **Tier 1 (Notify)**: Agent acts; human receives audit log
- **Tier 2 (Approve-on-Trigger)**: Agent flags specific action types for human approval
- **Tier 3 (Approve-All)**: Human approves every consequential action
- **Tier 4 (Supervised)**: Human leads, agent assists

**Financial services consensus**: AI processes at machine speed; human retains approval authority for any action with material quality or regulatory impact.

LangGraph implements HITL via `interrupt_before` or `interrupt_after` semantics — execution pauses, state is serialized, human receives approval request via webhook, approval/rejection resumes or terminates the workflow. All approval decisions are logged as first-class workflow events.

### Dead Letter Queue (DLQ) Pattern
Failed agent tasks that exhaust retries route to a DLQ with full execution context: input, attempted steps, error traces, tool call logs. DLQ items trigger alerting and can be replayed with modified inputs after human review. Temporal natively manages DLQ semantics via workflow failure states.

### Checkpoint / Resume
**Temporal** is the production-standard durable execution engine for long-running agents. Complete workflow history persisted; Activity retries resume from last Heartbeat payload; idempotency keys bound to workflow run ID prevent duplicate side effects on replay.

**Idempotency rule**: Any tool that writes external state must carry an idempotency key tied to the workflow run ID and step number.

### Reversibility Requirements
Before executing any irreversible action (send email, commit to database, call external API that cannot be undone), agents must either (a) have explicit approval in the task specification or (b) request human confirmation (Tier 2 HITL). Document reversibility level for every registered tool: fully reversible / reversible-with-cost / irreversible.

---

## Performance Management

### Core KPIs

**Task Completion Rate (TCR)**: Percentage of initiated agent runs that produce a valid, accepted outcome. Target: ≥85% for optimized production workflows. The gap between attempted and completed tasks is the true economic signal.

**Cost-Per-Successful-Task (CPST)**: Total agent operating cost (tokens + infrastructure + human review time) / accepted outcomes. The ROI-linked metric, not cost-per-token.

**Escalation Rate**: Percentage of tasks requiring human intervention. Target: ≤5% for mature, well-scoped agents. Higher rates signal prompt, tool, or task-scope issues.

**Time-to-First-Action (TTFA) / End-to-End Latency SLAs:**
- Customer-facing agents: ≤3 seconds response
- Back-office automation: 10–30 second processing window acceptable
- Batch/research agents: minutes acceptable

**Throughput**: Tasks processed per unit time. Capacity expansion ratio (throughput growth without proportional headcount growth) is the organizational KPI that validates ROI.

### Queue Management
Use priority queues: customer-facing urgent tasks ahead of batch background tasks. Implement backpressure: when downstream agents are saturated, upstream dispatchers slow intake rather than overwhelming queues. Capacity plan by profiling peak task arrival rate × average task duration × desired queue depth.

### Latency Optimization
- Parallel execution for independent subtasks (fan-out pattern)
- Streaming responses from LLMs (especially for voice pipelines)
- Caching repeated tool calls and knowledge retrieval
- Semantic caching for near-duplicate prompts (30–60% cache hit rates achievable)
- Pre-warming agent containers / warm instances for latency-sensitive paths

---

## Governance & Control

### Least-Privilege Agent Identity
Issue ephemeral, scoped credentials per task run rather than long-lived API keys. Bind permissions to the specific tool, dataset, and action the agent is authorized to invoke. Planner agents typically require no tools; tool access is granted only to execution agents. **Microsoft Entra Agent IDs** implement this natively in Azure-ecosystem agents.

**Microsoft Agent Governance Toolkit** (open source, covers all 10 OWASP Agentic Top 10): policy enforcement, zero-trust identity, execution sandboxing, and reliability engineering patterns.

### Audit Logging Requirements
Minimum compliant audit log record:
```
{agent_id, run_id, step_number, tool_called, tool_arguments, tool_response, timestamp, user_id, cost_tokens}
```
Both the request context AND the policy decision must be logged. Required for SOC 2, HIPAA, and EU AI Act compliance logging.

### Sandboxing
- Code execution agents: containerized sandbox (Docker, Firecracker microVM) with no network access except explicitly allowlisted endpoints
- File system access: ephemeral volume per run, destroyed after task completion
- Tool invocation: runtime policy evaluation at each tool call, not just at initialization

---

## Agent Memory Systems

### Memory Taxonomy
| Memory Type | Storage | Purpose | Implementation |
|-------------|---------|---------|----------------|
| **Working memory** | Context window | Current task state | Prompt/context management |
| **Episodic memory** | External DB | Concrete, timestamped events | Mem0, Zep, LangMem; BM25 or temporal query retrieval |
| **Semantic memory** | Vector DB | High-level facts, summaries | pgvector, Pinecone, Weaviate; cosine similarity retrieval |
| **Procedural memory** | Model weights/prompt cache | "How to do X" | Fine-tuning (LoRA), system prompts |

**2025 research finding**: Chronological Awareness Score (tracking how entities change over time) is the weakest dimension across current architectures — agents recall facts well but struggle with temporal ordering of events. Design episodic memory with explicit timestamps and temporal query support.

### Retrieval Hierarchy
```
Context window (immediate) 
  → Vector similarity search (semantic, ~ms)
  → BM25 keyword search (episodic, ~ms)  
  → Graph traversal (relational context, ~100ms)
  → Full-text database (archival, ~seconds)
```

### Memory Consolidation
Run nightly consolidation jobs: compress episodic logs into semantic summaries using a cheap model (Haiku/Mini tier), embed summaries into the semantic store, archive raw episodes. Prevents unbounded vector store growth while preserving retrievable knowledge. Always preserve ground-truth episodic records alongside summaries to prevent semantic drift.

---

## Evaluation & QA

### Production Benchmark Suite
- **GAIA** (450 questions, multimodal, tool use): general capability assessment
- **SWE-bench**: GitHub issue resolution for code agents
- **WebArena**: web-based task execution for browser agents
- **AgentBench**: multi-step, interactive environments for planning + tool use

Single headline success metric is insufficient; report multiple complementary dimensions.

### Automated Testing Practices
**Regression testing**: Golden test set of ≥100 agent runs with verified correct trajectories and outcomes. Run on every prompt or tool change. Alert on TCR drop >5% or trajectory divergence.

**Shadow testing**: Route production traffic to both current and candidate agent versions; compare outcomes without exposing users to the new version.

**Load testing**: Stress-test full stack (orchestration, tool APIs, vector store, LLM APIs) at 2–5× expected peak throughput. Identify queue saturation points, timeout cascades, and memory store degradation.

**Red-teaming**: Adversarial inputs targeting prompt injection, tool misuse, scope violations, jailbreaks. OWASP Agentic Top 10 provides the attack taxonomy.

---

## Production Deployment

### Infrastructure Patterns

**Containerized Agents (Kubernetes)**: Package each agent type as a Docker container. Use KEDA (Kubernetes Event-Driven Autoscaling) triggered by queue depth rather than CPU/memory — LLM-calling agents are I/O-bound. Sidecar containers handle observability (OpenTelemetry collector) and secrets injection.

**Serverless Agents**: AWS Lambda / Google Cloud Functions for stateless, short-duration tasks (<15 min). Keep warm instances for latency-sensitive agents. Bedrock AgentCore provides managed serverless runtime without managing Lambda layer directly.

**Async Execution Engines**:
- **Temporal**: Production standard for long-running, stateful, durable agent workflows. Handles retry, timeout, checkpointing, and saga patterns natively. Growing adoption in 2025-2026.
- **Celery + Redis/SQS**: Simpler, but requires manual implementation of idempotency and saga patterns. Appropriate for simple task queues.
- **Inngest / Restate**: Emerging alternatives with lighter operational overhead.

### Agent Versioning
Version the triad: (prompt template, tool set, underlying model). A change to any element increments the version. Use semantic versioning: `{agent_name}:{major}.{minor}.{patch}`.

**Canary rollout**: Deploy to 5% traffic → monitor 6 hours against automated gates (error rate, latency, tool call patterns, quality score) → expand to 25% → 24h → 100%. Automate rollback if gates fail.

**Agent Registry**: Central registry of deployed agents with: version, endpoint, capability descriptions, tool permissions, owner, SLA commitments, cost budget, and rollback target.

---

## Cost Management

### Token Budget Architecture
**Quadratic token growth trap**: Multi-turn agents accumulate context linearly per turn. 10 turns × 4 agents = 40 LLM calls each seeing the growing history. Budget controls must trigger before context overflow, not after.

Enforce per-run token budgets at the orchestration layer (not relying on agent self-reporting). Budget exhaustion → graceful termination with partial result.

### Model Tiering (Critical Cost Lever)
Distribution of real workloads:
- ~60%: cheap small models (GPT-4o Mini, Claude Haiku) — formatting, extraction, routing decisions, simple classification
- ~20%: mid-tier (30–70B parameter models)
- ~20%: flagship models for complex reasoning, synthesis, ambiguous judgment

**Saving**: ~75% of LLM calls in a typical agent are routing decisions that don't require a premium model. Route these to cheap models; reserve premium for synthesis steps. Achievable savings: 70–80% cost reduction without measurable quality drop.

### Caching Strategies
- **Prompt caching**: Anthropic and OpenAI offer caching for repeated system prompts and context prefixes
- **Semantic caching**: Cache LLM responses for near-duplicate queries; 30–60% hit rates on production workloads
- **Tool result caching**: Cache idempotent tool calls (read-only API calls) with TTL matched to data freshness

### FinOps for Agent Systems
Tag every LLM call: `{agent_id, workflow_id, task_type, model_tier, user_segment}`. In 2026, 98% of FinOps practitioners are responsible for AI spend. 73% of enterprises report AI costs exceeded original projections — budget for 2–3× initial estimates until workload patterns stabilize.

---

## Team Structure for Agentic Systems

### Core Roles

**Agent Engineer** (Sr. SWE specialization): Owns infrastructure under deployed agent systems — model versioning, prompt deployment pipelines, evaluation cadence, incident response. 2026 US salary: $155K–$275K. Most under-invested role after shipping agent demos.

**Memory & State Auditor**: Owns memory system architecture, monitors for memory drift, manages consolidation pipelines, ensures memory privacy/retention compliance.

**Safety & Escalation Reviewer**: Owns HITL escalation workflows, reviews escalated agent runs, feeds back learnings to improve agent scope definition.

**AI Reliability Engineer (AIRE)**: SRE analog for agent systems. Owns incident response playbooks, manages observability stack, operates DLQs, conducts post-mortems on agent misbehaviors.

### Incident Response Severity Tiers
- **P0**: Agent executing irreversible actions outside approved scope → immediate kill-switch, block run class, post-mortem within 24h
- **P1**: TCR drop >20% → rollback to previous version, root cause analysis
- **P2**: Cost spike >2× baseline → budget circuit breaker activated, throttle throughput
- **P3**: Quality degradation flagged by sampling → shadow test candidate fix before canary

AI-assisted incident response case study (USENIX SREcon25): reduced mean time to resolution from 4 hours to 8 minutes for known failure patterns.

### Documentation Practices
Document the agent triad (prompt, tools, model) in version control. Maintain a runbook per deployed agent: failure modes, escalation contacts, rollback procedure, cost budget, SLA commitments. Store evaluation results alongside code (eval-as-code), not in spreadsheets.

---

## Emerging Patterns (2025–2026)

### Agent-to-Agent Protocol (A2A)
Google released A2A on April 9, 2025; donated to Linux Foundation shortly after. 150+ supporting organizations. A2A is the layer above MCP: MCP connects agents to tools, A2A connects agents to other agents.

Technical: JSON-RPC over HTTPS, Server-Sent Events for streaming. Five task lifecycle states: `submitted → working → input-required → completed → failed`. Each agent publishes an Agent Card (JSON manifest): capabilities, authentication requirements, supported interaction modes. Discovery via Agent Card lookup enables dynamic orchestration where orchestrators discover and delegate at runtime.

### Long-Running Agents with Sleep/Wake
2026 operational pattern: agents that run for hours or days, sleeping between decision points. Temporal's durable execution handles sleep/wake — a sleeping agent consumes no compute but maintains full state. Wake triggers: scheduled timer, external event (webhook), upstream agent completing a dependency, or human approval. Enables multi-day workflows (procurement, compliance review, research synthesis) without holding open LLM connections.

### Computer-Use Agents in Production
Industry benchmark: most agents cluster at 40–60% success rate on OSWorld in early 2026. Reliable production use cases are narrow: form filling in controlled enterprise systems, structured data extraction from known UI patterns, automated testing of web applications. Infrastructure requirements: dedicated browser sandbox per session (Browserbase), screenshot-at-every-step audit trail, strict session time limits.

### Voice Agent Architecture
Production stack: `WebRTC/telephony bridge → Streaming STT → LLM with tool use → Streaming TTS → output`

Latency targets for satisfactory conversation: total end-to-end ≤1 second.
- STT (Deepgram Nova, AssemblyAI): 90–150ms
- LLM streaming (first token): 350ms–1s
- TTS streaming (ElevenLabs): 75–200ms

Hard orchestration problems: turn-taking detection, tool call interruption handling, human handoff (warm transfer mid-conversation), compliance recording.

---

## Quick-Reference Decision Heuristics

| Decision | Guidance |
|----------|----------|
| Framework choice | Data location + compliance requirements drive platform; task complexity drives framework |
| When to use Temporal | Any agent task >5 min, multi-step, or requiring guaranteed execution |
| Memory type | Episodic for "what happened," semantic for "what's true," procedural for "how to do X" |
| Model tier for subtask | If a human intern could do it in 30 seconds, use Mini/Haiku |
| Rollout strategy | Always canary; never direct-to-production for prompt changes |
| HITL threshold | Any irreversible action, any financial commitment, any regulatory-adjacent decision |
| Audit log minimum | Every tool call with full arguments, timestamp, agent ID, cost |
| TCR below 80% | Treat as incident: instrument trajectory, identify failure mode, fix before scaling |
| Cost spike | Check for quadratic context growth first, then tool call loops, then model tier misrouting |
