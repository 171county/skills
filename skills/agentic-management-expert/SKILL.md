---
name: agentic-management-expert
description: Activate this skill when the user asks about managing, orchestrating, designing, or operating AI agent systems in production. Covers agentic orchestration patterns (supervisor-worker, hierarchical, pipeline, swarm, debate), multi-agent lifecycle management, task decomposition strategies, human-in-the-loop (HITL) design, reliability engineering for agents (circuit breakers, retry logic, fallbacks), agent state and memory management (short-term, long-term, episodic, semantic), LangGraph checkpointing and resumability, OpenTelemetry GenAI semantic conventions, cost governance (model routing, token budgets, per-task cost tracking), agent evaluation pipelines (task completion rate, human intervention rate, cost-per-task), security for agentic systems (OWASP Agentic AI Top 10, prompt injection defense, E2B sandboxing, supply chain), organizational governance (agentic maturity model, agent registry, fleet management), building agent teams, multi-agent communication protocols (MCP, A2A), debugging and observability for agentic workflows, production deployment patterns, and scaling agent fleets. Use for questions about running agents reliably, safely, and cost-effectively at scale.
---

# Agentic Management Expert

You are a world-class agentic systems architect and operations engineer. You design, deploy, and manage production AI agent systems that are reliable, observable, cost-efficient, and secure. You synthesize expertise from distributed systems engineering, LLM operations, and organizational management to help teams run agent fleets that actually work in the real world.

---

## 1. Orchestration Patterns

### Core Patterns (choose based on task structure)

| Pattern | Structure | Best For | Failure Mode |
|---|---|---|---|
| **Sequential Pipeline** | A → B → C → D | Document processing, ETL, structured workflows | Single node failure kills pipeline |
| **Supervisor-Worker** | Supervisor dispatches to specialist workers | Research, code review, multi-domain tasks | Supervisor bottleneck; worker coordination |
| **Hierarchical** | CEO → Manager → Worker (tree) | Complex projects needing multi-level planning | Latency compounds through levels |
| **Parallel Fan-Out** | Orchestrator → N parallel agents → aggregator | Speed-critical tasks with independent subtasks | Aggregation logic complexity |
| **MapReduce** | Map agents → Reduce agent | Large-scale analysis, data synthesis | Reducer must handle partial failures |
| **Critic-Refiner** | Generator ↔ Critic (iterative) | Writing, code quality, reasoning quality | Infinite loop risk without convergence check |
| **Debate/Society** | N agents argue → Arbitrator decides | High-stakes decisions, bias reduction | Cost explosion; arbitration quality |
| **Swarm** | Decentralized agents, emergent behavior | Exploration tasks, no clear structure | Unpredictability; hard to audit |
| **ReAct Loop** | Reason → Act → Observe (single agent) | Tool-using tasks, retrieval-augmented | Context window overflow |
| **Plan-and-Execute** | Planner → Executor (separate) | Long-horizon tasks, upfront planning | Rigid plans fail in dynamic environments |
| **ReWOO** | Reason → Plan → Execute (no interleaving) | Reduces LLM calls; faster parallel execution | Harder to adapt mid-stream |
| **Tree of Thoughts** | Branching + pruning | Puzzles, math, requires backtracking | High token cost; complex state management |
| **Reflexion** | Agent reflects on failures → retry | Self-improving quality | Token cost; may not converge |
| **LATS (Tree + ReAct)** | Monte Carlo Tree Search + LLM | Complex reasoning benchmarks | Compute-intensive |
| **Mixture of Agents (MoA)** | Multiple LLMs → aggregation layer | Ensemble quality, routing diversity | Latency; coordination overhead |

### LangGraph Implementation (canonical 2025 pattern)

```python
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.postgres import PostgresSaver
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]
    task_id: str
    iteration: int
    result: str | None
    error: str | None

def build_agent_graph(checkpointer: PostgresSaver) -> StateGraph:
    graph = StateGraph(AgentState)
    
    graph.add_node("planner", planner_node)
    graph.add_node("executor", executor_node)
    graph.add_node("critic", critic_node)
    graph.add_node("human_review", human_review_node)
    
    graph.set_entry_point("planner")
    graph.add_edge("planner", "executor")
    graph.add_conditional_edges("executor", route_after_execution, {
        "critic": "critic",
        "human_review": "human_review",
        "done": END,
    })
    graph.add_conditional_edges("critic", route_after_critic, {
        "retry": "executor",
        "done": END,
    })
    graph.add_edge("human_review", "executor")
    
    return graph.compile(checkpointer=checkpointer, interrupt_before=["human_review"])

# Resumable execution with checkpointing
async def run_with_resume(graph, task_id: str, input_state: AgentState):
    config = {"configurable": {"thread_id": task_id}}
    
    async for event in graph.astream(input_state, config=config):
        yield event  # Stream intermediate steps
    
    # Resume after HITL approval
    snapshot = await graph.aget_state(config)
    if snapshot.next == ("human_review",):
        await graph.aupdate_state(config, {"approved": True})
        async for event in graph.astream(None, config=config):
            yield event
```

---

## 2. Task Decomposition Strategies

### Decomposition Methods

**Functional Decomposition**: Break by function type (research, write, review, publish). Best for creative/content workflows.

**Domain Decomposition**: Break by knowledge domain (legal, technical, financial). Best for multi-expert tasks.

**Data Decomposition**: Break by data partitions (N records → N workers). Best for bulk processing.

**Temporal Decomposition**: Break by time sequence (today's tasks, this week's). Best for planning agents.

### Decomposition Quality Checklist
- [ ] Each subtask is independently verifiable
- [ ] Dependencies between subtasks are explicitly modeled (DAG)
- [ ] Subtasks have clear success criteria (not just "do X" but "X with quality metric Y")
- [ ] Estimated token budget per subtask fits in model context
- [ ] Failure of one subtask doesn't silently corrupt others
- [ ] Subtask granularity matches model capability (don't over-decompose)

### Avoiding Decomposition Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| Over-decomposition | 47 agents for a 3-step task | Merge: if two agents always call each other, combine |
| Under-decomposition | One agent with 15 tools | Split by tool category or domain |
| Circular dependencies | Agent A needs B's output; B needs A's | Introduce mediator or merge |
| God orchestrator | Orchestrator contains all business logic | Push logic to specialized agents |
| Silent failure | Subtask returns empty string, pipeline continues | Add output validators after each node |

---

## 3. Human-in-the-Loop (HITL) Design

### HITL Decision Framework

**Always require HITL for**:
- Irreversible external actions (send email, execute payment, deploy code to prod, delete data)
- Actions above a cost threshold (configurable, e.g., >$500 spend)
- Confidence score below threshold (track calibrated confidence in your eval pipeline)
- Novel situations outside training distribution (detect via embedding distance from known examples)
- Safety-critical domains (medical, legal advice delivery, financial decisions)

**Configurable HITL triggers**:
```python
class HITLConfig(BaseModel):
    require_approval_for_external_actions: bool = True
    cost_threshold_usd: float = 10.0
    confidence_threshold: float = 0.85
    max_iterations_before_escalation: int = 3
    escalation_channels: list[str] = ["slack", "email"]
    sla_minutes: int = 60  # Auto-escalate if no response
```

### HITL UX Patterns

**Approval Queue**: Async queue where humans review pending agent actions. Agent blocks/pauses.
```
┌─────────────────────────────────────────┐
│ Agent Action Pending Review             │
│ Task: Send contract to client@acme.com  │
│ Contract: MSA_v2_final.pdf (47 pages)   │
│ Risk: HIGH (external; irreversible)     │
│ [View Contract] [Approve] [Reject] [Edit]│
│ Auto-escalates in: 47 minutes           │
└─────────────────────────────────────────┘
```

**Inline Editing**: Human can edit agent output before it's used downstream.

**Confidence Display**: Always show agent's self-reported confidence + the evidence it used to reach conclusion.

**Audit Trail**: Every HITL decision stored with: decision, actor, timestamp, reasoning, downstream outcome.

### HITL Anti-Patterns
- "Approve everything" fatigue → automate low-risk approvals; only surface high-risk
- Missing SLA escalation → agent blocks indefinitely; always implement timeout + fallback
- No rejection feedback loop → agent never learns what humans reject; feed rejections to eval dataset

---

## 4. Reliability Engineering

### Circuit Breaker Pattern for Agents

```python
import asyncio
from enum import Enum
from datetime import datetime, timedelta

class CircuitState(Enum):
    CLOSED = "closed"      # Normal operation
    OPEN = "open"          # Failing; reject all calls
    HALF_OPEN = "half_open"  # Testing recovery

class AgentCircuitBreaker:
    def __init__(self, failure_threshold=5, recovery_timeout=60, success_threshold=2):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.success_threshold = success_threshold
        self.failure_count = 0
        self.success_count = 0
        self.state = CircuitState.CLOSED
        self.last_failure_time: datetime | None = None
    
    async def call(self, func, *args, **kwargs):
        if self.state == CircuitState.OPEN:
            if datetime.now() - self.last_failure_time > timedelta(seconds=self.recovery_timeout):
                self.state = CircuitState.HALF_OPEN
            else:
                raise Exception("Circuit breaker OPEN — agent unavailable")
        
        try:
            result = await func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        if self.state == CircuitState.HALF_OPEN:
            self.success_count += 1
            if self.success_count >= self.success_threshold:
                self.state = CircuitState.CLOSED
                self.failure_count = 0
                self.success_count = 0
    
    def _on_failure(self):
        self.failure_count += 1
        self.last_failure_time = datetime.now()
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN
```

### Retry Strategy

```python
import asyncio
import random

async def retry_with_backoff(
    func,
    max_retries: int = 3,
    base_delay: float = 1.0,
    max_delay: float = 60.0,
    jitter: bool = True,
    retryable_exceptions: tuple = (Exception,)
):
    for attempt in range(max_retries + 1):
        try:
            return await func()
        except retryable_exceptions as e:
            if attempt == max_retries:
                raise
            delay = min(base_delay * (2 ** attempt), max_delay)
            if jitter:
                delay *= (0.5 + random.random())
            await asyncio.sleep(delay)
```

### Idempotency Keys

Every agent action that touches external systems MUST use idempotency keys:
```python
import uuid
import hashlib

def generate_idempotency_key(task_id: str, action_type: str, payload_hash: str) -> str:
    """Deterministic key: same task + action + payload always produces same key."""
    content = f"{task_id}:{action_type}:{payload_hash}"
    return hashlib.sha256(content.encode()).hexdigest()[:32]
```

### Fallback Strategies

| Failure Type | Primary Fallback | Secondary Fallback |
|---|---|---|
| LLM API timeout | Retry with smaller model | Return cached/partial result |
| Tool execution failure | Try alternative tool | Return structured error to orchestrator |
| Context overflow | Summarize + truncate history | Restart with summary only |
| External API down | Use cached data (TTL-stamped) | Human escalation |
| Hallucinated tool call | Validate schema; reject invalid | Re-prompt with correction |

---

## 5. Observability: OpenTelemetry GenAI

### GenAI Semantic Conventions (OTel 1.29+)

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Setup
provider = TracerProvider()
provider.add_span_processor(
    BatchSpanProcessor(OTLPSpanExporter(endpoint="http://otel-collector:4317"))
)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer("agent.system", "1.0.0")

# Instrument an LLM call
with tracer.start_as_current_span("llm.call") as span:
    span.set_attribute("gen_ai.system", "anthropic")
    span.set_attribute("gen_ai.request.model", "claude-opus-4-8")
    span.set_attribute("gen_ai.request.max_tokens", 4096)
    span.set_attribute("gen_ai.request.temperature", 0.7)
    
    response = await call_llm(messages, model="claude-opus-4-8")
    
    span.set_attribute("gen_ai.response.model", response.model)
    span.set_attribute("gen_ai.usage.input_tokens", response.usage.input_tokens)
    span.set_attribute("gen_ai.usage.output_tokens", response.usage.output_tokens)
    span.set_attribute("gen_ai.response.finish_reason", response.stop_reason)

# Instrument a tool call
with tracer.start_as_current_span("agent.tool.call") as span:
    span.set_attribute("agent.tool.name", "web_search")
    span.set_attribute("agent.tool.input", json.dumps(tool_input))
    span.set_attribute("agent.task.id", task_id)
    span.set_attribute("agent.iteration", iteration_count)
```

### Key Metrics to Instrument

```python
from opentelemetry import metrics

meter = metrics.get_meter("agent.system")

# Counters
task_completion_counter = meter.create_counter("agent.task.completed")
task_failure_counter = meter.create_counter("agent.task.failed")
hitl_trigger_counter = meter.create_counter("agent.hitl.triggered")
tool_call_counter = meter.create_counter("agent.tool.calls")

# Histograms
task_duration = meter.create_histogram("agent.task.duration_ms")
llm_latency = meter.create_histogram("agent.llm.latency_ms")
token_usage = meter.create_histogram("agent.token.usage")

# Gauges
active_agents = meter.create_gauge("agent.fleet.active")
queue_depth = meter.create_gauge("agent.queue.depth")
cost_per_hour = meter.create_gauge("agent.cost.per_hour_usd")
```

### Recommended Observability Stack

| Layer | Tool | Purpose |
|---|---|---|
| Tracing | Jaeger / Tempo | Distributed trace visualization |
| Metrics | Prometheus + Grafana | Dashboards, alerting |
| Logs | Loki / Elasticsearch | Structured log aggregation |
| LLM-specific | LangSmith / Langfuse | Prompt/response replay, eval |
| Cost tracking | Custom + OTel | Per-task, per-model cost attribution |
| Alerting | PagerDuty / OpsGenie | On-call for agent failures |

---

## 6. State & Memory Management

### Four-Tier Memory Architecture

```
Tier 1: In-context (ephemeral)
  └── Current conversation window; lost on completion
  └── Use for: immediate reasoning, tool call results

Tier 2: Working memory (session-scoped)
  └── LangGraph state / Redis (TTL: session duration)
  └── Use for: intermediate results, plan state, iteration counts

Tier 3: Episodic memory (task-scoped)
  └── PostgreSQL checkpoints / vector store summaries (TTL: weeks-months)
  └── Use for: task history, agent reflections, prior decisions

Tier 4: Semantic memory (long-term)
  └── Knowledge graph / vector DB / structured DB (TTL: permanent)
  └── Use for: facts, procedures, user preferences, learned patterns
```

### Production Memory Patterns

**Mem0 Integration** (managed memory layer):
```python
from mem0 import MemoryClient

client = MemoryClient(api_key=os.environ["MEM0_API_KEY"])

# Store memory after task completion
client.add(
    messages=conversation_history,
    user_id=user_id,
    agent_id="research-agent",
    metadata={"task_type": "research", "domain": "finance"}
)

# Retrieve relevant memories before task execution
memories = client.search(
    query=task_description,
    user_id=user_id,
    limit=10,
    rerank=True  # Semantic reranking
)
```

**Zep** (graph-based memory):
```python
from zep_cloud.client import AsyncZep

zep = AsyncZep(api_key=os.environ["ZEP_API_KEY"])

# Add session facts to graph
await zep.graph.add(
    user_id=user_id,
    type="text",
    data=f"User prefers detailed technical explanations. Last task: {task_summary}"
)

# Search graph memory
results = await zep.graph.search(
    user_id=user_id,
    query=current_task,
    scope="nodes",
    limit=5
)
```

### Context Window Management

```python
class ContextManager:
    def __init__(self, max_tokens: int = 100_000, reserve_tokens: int = 4096):
        self.max_tokens = max_tokens
        self.reserve_tokens = reserve_tokens
        self.available = max_tokens - reserve_tokens
    
    async def fit_messages(self, messages: list[dict], summarizer) -> list[dict]:
        """Truncate/summarize history to fit context budget."""
        total = count_tokens(messages)
        if total <= self.available:
            return messages
        
        # Keep system prompt + last N messages; summarize middle
        system = [m for m in messages if m["role"] == "system"]
        recent = messages[-6:]  # Last 3 turns
        middle = messages[len(system):-6]
        
        if middle:
            summary = await summarizer(middle)
            return system + [{"role": "assistant", "content": f"[Summary of prior context]: {summary}"}] + recent
        
        return system + recent
```

---

## 7. Security: OWASP Agentic AI Top 10 (2025)

### Threat Taxonomy

| # | Threat | Description | Mitigation |
|---|---|---|---|
| AA1 | Prompt Injection | Malicious content in tool outputs hijacks agent behavior | Input sanitization; separate trusted/untrusted channels; structured outputs |
| AA2 | Excessive Agency | Agent takes broader actions than authorized | Minimal tool scopes; action whitelist; HITL for high-risk |
| AA3 | Insecure Tool Integration | Tools with insufficient validation, overly permissive APIs | Schema validation; principle of least privilege; tool sandboxing |
| AA4 | Rug Pull Attack | Tool behavior changes after initial trust grant | Tool registry with hash verification; signed tool manifests |
| AA5 | Supply Chain Compromise | Malicious MCP server or agent dependency | Dependency scanning; MCP server registry with auditing |
| AA6 | Memory Poisoning | Injecting false facts into long-term memory | Memory integrity checks; provenance tracking; human memory review |
| AA7 | Intent Breaking | Subtle steering away from original task over many steps | Intent anchoring; periodic intent verification vs. original goal |
| AA8 | Cascading Failures | One agent's failure causes systemic collapse | Bulkheads; circuit breakers; bounded failure domains |
| AA9 | Sensitive Data Exfiltration | Agent leaks PII/secrets through tool calls or outputs | Output filtering; PII detection (Presidio); data loss prevention |
| AA10 | Uncontrolled Recursion | Agent spawns agents spawning agents (infinite loop) | Max depth limits; spawn rate limiting; circuit breakers on agent creation |

### E2B Code Sandboxing

```python
from e2b_code_interpreter import Sandbox

async def execute_agent_code(code: str, timeout: int = 30) -> dict:
    """Execute untrusted agent-generated code in isolated sandbox."""
    async with Sandbox(timeout=timeout) as sandbox:
        # No network access, no filesystem persistence, isolated process
        result = await sandbox.run_code(code)
        return {
            "stdout": result.logs.stdout,
            "stderr": result.logs.stderr,
            "error": result.error,
            "results": [r.text for r in result.results]
        }
```

### Supply Chain Defense (post-OpenClaw incident pattern)

```python
class MCPServerRegistry:
    """Verified registry of approved MCP servers with integrity checking."""
    
    def __init__(self, registry_url: str, signing_key: str):
        self.approved_servers: dict[str, ServerEntry] = {}
        self.signing_key = signing_key
    
    def verify_server(self, server_name: str, server_hash: str) -> bool:
        entry = self.approved_servers.get(server_name)
        if not entry:
            raise SecurityError(f"Unapproved MCP server: {server_name}")
        if entry.hash != server_hash:
            raise SecurityError(f"MCP server hash mismatch — possible tampering: {server_name}")
        if entry.revoked:
            raise SecurityError(f"MCP server has been revoked: {server_name}")
        return True
```

---

## 8. Cost Governance

### Model Routing (40-80% cost reduction)

```python
class ModelRouter:
    """Route tasks to cheapest capable model."""
    
    MODELS = {
        "claude-haiku-4-5": {"cost_per_1k_input": 0.00025, "cost_per_1k_output": 0.00125, "capability": 1},
        "claude-sonnet-4-6": {"cost_per_1k_input": 0.003, "cost_per_1k_output": 0.015, "capability": 2},
        "claude-opus-4-8": {"cost_per_1k_input": 0.015, "cost_per_1k_output": 0.075, "capability": 3},
    }
    
    def route(self, task: AgentTask) -> str:
        complexity = self.estimate_complexity(task)
        
        if complexity == "simple":  # Classification, extraction, summary <1000 tokens
            return "claude-haiku-4-5"
        elif complexity == "medium":  # Multi-step reasoning, tool use
            return "claude-sonnet-4-6"
        else:  # Deep research, complex code, multi-domain synthesis
            return "claude-opus-4-8"
    
    def estimate_complexity(self, task: AgentTask) -> str:
        if task.requires_tools and len(task.required_tools) > 3:
            return "complex"
        if task.estimated_output_tokens > 2000:
            return "medium"
        return "simple"
```

### Token Budget Management

```python
class TokenBudgetManager:
    def __init__(self, daily_budget_usd: float, alert_threshold: float = 0.8):
        self.daily_budget = daily_budget_usd
        self.alert_threshold = alert_threshold
        self.spent_today = 0.0
    
    def check_budget(self, estimated_cost: float) -> bool:
        if self.spent_today + estimated_cost > self.daily_budget:
            raise BudgetExceeded(f"Daily budget ${self.daily_budget} would be exceeded")
        if self.spent_today / self.daily_budget > self.alert_threshold:
            self.alert_ops(f"Budget at {self.spent_today/self.daily_budget:.0%}")
        return True
    
    def record_usage(self, input_tokens: int, output_tokens: int, model: str):
        rates = ModelRouter.MODELS[model]
        cost = (input_tokens/1000 * rates["cost_per_1k_input"] + 
                output_tokens/1000 * rates["cost_per_1k_output"])
        self.spent_today += cost
        return cost
```

### Cost Attribution Pattern

Every LLM call must be tagged with: `task_id`, `agent_id`, `user_id`, `tenant_id`, `workflow_name`. This enables per-customer, per-workflow cost reporting.

---

## 9. Agent Evaluation Pipelines

### Core KPIs

| Metric | Formula | Target | Red Flag |
|---|---|---|---|
| **Task Completion Rate (TCR)** | Completed / Total attempts | >90% | <75% |
| **Human Intervention Rate (HIR)** | HITL triggers / Total tasks | <10% | >25% |
| **Cost Per Task (CPT)** | Total LLM cost / Tasks completed | Depends on task type | >2× baseline |
| **Task Duration (P50/P95)** | Time from start to completion | Depends on task type | P95 > 3× P50 |
| **Tool Call Success Rate** | Successful calls / Total calls | >95% | <85% |
| **Hallucination Rate** | Tasks with factual errors / Total | <5% | >15% |
| **Retry Rate** | Retried tasks / Total | <15% | >30% |

### Evaluation Framework

```python
from dataclasses import dataclass

@dataclass
class AgentEvalResult:
    task_id: str
    success: bool
    completion_time_ms: int
    total_cost_usd: float
    iterations: int
    hitl_triggered: bool
    tool_calls: list[dict]
    output_quality_score: float  # 0-1, from judge LLM
    factual_accuracy: float  # 0-1, from fact-checker
    errors: list[str]

class AgentEvaluator:
    def __init__(self, judge_llm, fact_checker, ground_truth_db):
        self.judge = judge_llm
        self.fact_checker = fact_checker
        self.ground_truth = ground_truth_db
    
    async def evaluate(self, task: AgentTask, result: AgentResult) -> AgentEvalResult:
        quality_score = await self.judge.score(
            task=task.description,
            output=result.output,
            criteria=["accuracy", "completeness", "actionability"]
        )
        
        factual_score = await self.fact_checker.verify(
            claims=result.extracted_claims,
            ground_truth=self.ground_truth.get(task.id)
        )
        
        return AgentEvalResult(
            task_id=task.id,
            success=result.success,
            completion_time_ms=result.duration_ms,
            total_cost_usd=result.cost,
            iterations=result.iterations,
            hitl_triggered=result.hitl_triggered,
            tool_calls=result.tool_calls,
            output_quality_score=quality_score,
            factual_accuracy=factual_score,
            errors=result.errors
        )
```

---

## 10. Organizational Governance

### Agentic Maturity Model (5 levels)

| Level | Name | Description | Signals |
|---|---|---|---|
| 0 | **Ad Hoc** | Individual agents, no process | Manual runs; no monitoring |
| 1 | **Managed** | Agents in prod, basic logging | Error alerts; manual cost review |
| 2 | **Defined** | Standardized patterns, HITL | Agent registry; runbooks; SLAs |
| 3 | **Measured** | KPI dashboards, eval pipelines | TCR/HIR/CPT tracked; A/B tested |
| 4 | **Optimizing** | Self-improving; automated eval | Continuous fine-tuning from HITL feedback |

### Agent Registry (mandatory at Level 2+)

```yaml
# agent-registry.yaml
agents:
  research-agent-v2:
    version: "2.3.1"
    owner: "platform-team"
    description: "Multi-step web research with citation extraction"
    model: "claude-sonnet-4-6"
    tools: ["web_search", "url_fetch", "document_parser"]
    max_iterations: 10
    max_cost_per_task_usd: 0.50
    hitl_required_for: ["external_publish", "high_confidence_claims"]
    sla:
      p50_ms: 15000
      p95_ms: 45000
    contacts:
      oncall: "#agent-ops"
      escalation: "platform-lead@company.com"
```

### Multi-Agent Communication Protocols

**MCP (Model Context Protocol)**: Tool layer; agents expose capabilities as MCP servers. Best for tool sharing between agents.

**A2A (Agent-to-Agent Protocol)**: Google's open protocol for agent-to-agent task delegation. Uses JSON-RPC over HTTP(S). Enables: task cards (structured requests), streaming responses, push notifications, multi-turn conversations between agents.

**Direct API**: For tightly coupled agents within the same system. Simplest; no protocol overhead.

**Message Queue (Kafka/SQS)**: For loosely coupled, async agent communication at scale. Best for high-throughput pipelines.

---

## 11. Production Deployment Patterns

### Agent Fleet Architecture

```
┌──────────────────────────────────────────────┐
│                 API Gateway                   │
│         (rate limiting, auth, routing)        │
└──────────────────────┬───────────────────────┘
                       │
┌──────────────────────▼───────────────────────┐
│           Task Orchestration Layer            │
│     (LangGraph server / custom queue)         │
│  ┌─────────────┐  ┌─────────────────────────┐│
│  │ Task Router │  │  HITL Approval Queue    ││
│  └──────┬──────┘  └─────────────────────────┘│
└─────────┼────────────────────────────────────┘
          │
   ┌──────▼──────────────────────────┐
   │         Agent Worker Pool       │
   │  ┌──────┐ ┌──────┐ ┌──────┐   │
   │  │Agent1│ │Agent2│ │AgentN│   │
   │  └──────┘ └──────┘ └──────┘   │
   └──────────────────────────────┘
          │
   ┌──────▼──────────────────────────┐
   │       Shared Services Layer     │
   │  Memory │ Tools │ LLM APIs      │
   │  Store  │ Svc   │ (w/ caching)  │
   └─────────────────────────────────┘
```

### Kubernetes Deployment for Agent Workers

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: agent-worker
spec:
  replicas: 5
  template:
    spec:
      containers:
      - name: agent
        image: company/agent-worker:2.3.1
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "2Gi"
            cpu: "1000m"
        env:
        - name: MAX_CONCURRENT_TASKS
          value: "3"
        - name: TOKEN_BUDGET_DAILY_USD
          value: "50.00"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          failureThreshold: 3
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: agent-worker-hpa
spec:
  scaleTargetRef:
    kind: Deployment
    name: agent-worker
  minReplicas: 2
  maxReplicas: 20
  metrics:
  - type: External
    external:
      metric:
        name: queue_depth
      target:
        type: AverageValue
        averageValue: 5  # Scale up when queue > 5 tasks per replica
```
