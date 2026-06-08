---
name: agentic-management-expert
description: Expert-level agentic AI system design and management — agent architectures (ReAct, Plan-and-Execute, Reflexion, CodeAct), multi-agent patterns (Supervisor/Worker, Swarm, Pipeline), memory systems, tool design and reliability, production deployment with async queues and Temporal, LangGraph state machines, evaluation frameworks, cost optimization, and failure pattern prevention. Use when designing, building, debugging, or operating agentic AI systems in production.
---

# Agentic Management Expert

## Core Agent Architectures

### ReAct (Reasoning + Acting)
- **Pattern**: Thought → Action → Observation → Thought (loop until done)
- Foundation for most tool-using agents; enables LLM to dynamically select tools
- **Implementation**: system prompt encodes the ReAct loop structure; tool results are "Observations"
- Limitation: no backtracking; each step committed; error propagation in long chains
- Best for: straightforward tool-use tasks, web search, database queries

### Plan-and-Execute
- **Pattern**: Planning step (decompose task into subtasks) → Execute each subtask independently
- Enables parallelization of independent subtasks
- Planner can use more expensive model; executors use cheaper models
- Re-planning loop: executor returns result → planner evaluates → re-plan if needed
- Best for: complex research tasks, code generation pipelines, multi-step data processing

### MRKL (Modular Reasoning, Knowledge, and Language)
- Neuro-symbolic routing: LLM router selects appropriate specialized module
- Modules: calculators, databases, APIs, other LLMs
- Router prompt includes module descriptions; LLM outputs module name + args
- Best for: systems with heterogeneous tool types requiring intelligent routing

### Reflexion (NeurIPS 2023)
- **Pattern**: Act → Evaluate → Reflect (verbal self-reflection) → Act again
- 91% on HumanEval (coding benchmark); major improvement from 68% baseline
- Reflexion memory: maintain list of prior attempts + reflections; include in context
- **Cost**: 2-3x token overhead vs. single-pass
- Best for: coding tasks, debugging, tasks with clear success criteria
- Limitation: reflection quality depends on evaluation quality; circular failure possible

### ReWOO (Reasoning WithOut Observation)
- **Innovation**: generate complete plan upfront before any tool execution
- Tool call outputs substituted back into plan after all tools execute
- **5x fewer LLM calls** vs. ReAct for same multi-tool tasks
- Best for: fixed, predictable tool sequences; cost-sensitive applications

### CodeAct
- **Pattern**: Agent writes Python code as action; executes in sandboxed interpreter
- Code = expressive, composable, debuggable action language
- Single action can do multi-step computation (loops, conditionals, data manipulation)
- OpenDevin/SWE-bench performance: code execution enables complex software tasks
- Sandbox: Jupyter kernel, Docker exec, Modal, E2B
- Best for: data analysis, code generation, computational tasks

### AutoGPT-Style Loops (Legacy — Avoid in Production)
- Autonomous goal pursuit with self-generated tasks
- Problem: infinite loop tendency, goal drift, cost explosion
- **Superseded by**: LangGraph checkpointing + HITL interrupts
- Historical context only; do not use as primary architecture in 2025-2026

---

## Multi-Agent Patterns

### Supervisor / Worker (Orchestrator-Subagent)
```
Supervisor Agent
├── Worker Agent A (specialized task)
├── Worker Agent B (specialized task)
└── Worker Agent C (specialized task)
```
- Supervisor: planning, delegation, result aggregation
- Workers: specialized, limited scope, return structured results
- Communication: JSON messages with task description + constraints + expected output format
- Best for: complex workflows with distinct specializations (research → writing → editing)

### Router Pattern
- Single dispatcher routes requests to specialized agents
- Routing criteria: task type, model capability, cost, latency
- Routing methods: LLM-based classification, embedding similarity, rule-based
- Best for: multi-domain chatbots, support systems

### Pipeline (Sequential)
- Agent A output → Agent B input → Agent C input
- Each agent sees only its immediate predecessor's output
- Validation between stages prevents error propagation
- Best for: structured data transformation, ETL pipelines, report generation

### Swarm (Peer-to-Peer)
- Agents can hand off to any other agent (OpenAI Swarm model)
- No central orchestrator; agents choose next agent based on task
- **Risks**: routing loops, unclear ownership of task completion
- Best for: conversational triage, helpdesk routing

### Event-Driven Multi-Agent
- Agents subscribe to event streams (Kafka, Redis Pub/Sub)
- React to events independently; emit new events on completion
- Decoupled: agents can be added/removed without restructuring
- Best for: real-time processing, IoT pipelines, financial event streams

### 2024-2026 Industry Reality
- 72% of enterprise AI projects are now multi-agent (up from 23% in 2024)
- Single-agent with good tool set handles 70% of tasks; multi-agent only when genuinely needed
- Most common failure: over-engineering with multi-agent when single-agent suffices

---

## Agent Memory Taxonomy

### Memory Types

**Sensory/Working Memory (In-Context)**
- Current conversation, recent tool outputs, scratchpad
- Limit: context window size; must manage carefully
- Eviction strategies: summarization, sliding window, recency-weighted compression
- Context compression: extract key facts → store in memory system → drop raw conversation

**Episodic Memory**
- Past interactions, completed tasks, prior attempts
- Storage: vector DB (semantic search) or structured DB (metadata filtering)
- Retrieval: embed query → semantic search over past episodes
- Use: RAG over agent's own history, few-shot examples from prior runs

**Semantic Memory**
- World knowledge, domain facts, entity relationships
- Storage: knowledge graph, vector DB, structured database
- Update: new knowledge injected without model retraining
- Use: product catalog, customer history, technical documentation

**Procedural Memory**
- Learned skills, task patterns, successful strategies
- Storage: system prompt injection, fine-tuning (expensive), few-shot examples
- Dynamic loading: select relevant procedures from library based on current task
- Use: successful API call patterns, debugging strategies that worked

**Conversation History Management**
```python
# Trim conversation to stay within context budget
def trim_messages(messages: list, max_tokens: int, tokenizer) -> list:
    total = 0
    trimmed = []
    for msg in reversed(messages):
        tokens = len(tokenizer.encode(msg["content"]))
        if total + tokens > max_tokens:
            break
        trimmed.insert(0, msg)
        total += tokens
    return trimmed
```

---

## Tool Design and Reliability

### Tool Design Principles
1. **Single responsibility**: each tool does one thing well
2. **Idempotent where possible**: calling twice = calling once (safe for retry)
3. **Descriptive names + descriptions**: LLM selects tools based on descriptions
4. **Explicit error states**: return structured errors, not Python exceptions
5. **Input validation**: validate before execution; return clear error messages
6. **Output schema**: define expected output structure; use `outputSchema` in MCP

**Tool Description Template**
```
Name: create_calendar_event
Description: Creates a new calendar event for a specific date and time. 
  Use this when the user wants to schedule a meeting, appointment, or event.
  Do NOT use for reminders (use create_reminder instead).
Parameters:
  - title (string, required): Event title
  - start_time (ISO 8601): Event start datetime
  - duration_minutes (int, default 30): Duration in minutes
  - attendees (array of emails, optional): Attendee email addresses
Returns: {"event_id": string, "calendar_url": string}
Errors: "CONFLICT" if time slot unavailable, "INVALID_DATE" if date is past
```

### Tool Failure Modes and Mitigations
| Failure | Mitigation |
|---------|-----------|
| Wrong tool selected | Better descriptions; add "do NOT use for X" |
| Wrong parameter extraction | Few-shot examples in description |
| Transient API errors | Exponential backoff with jitter |
| Validation errors | Don't retry; return error to LLM for correction |
| Timeout | Set explicit timeouts; return partial results |
| Rate limiting | Circuit breaker + queue backpressure |

**Exponential Backoff with Jitter (Standard)**
```python
import asyncio
import random

async def call_tool_with_retry(tool_fn, *args, max_retries=4):
    for attempt in range(max_retries):
        try:
            return await tool_fn(*args)
        except TransientError:
            wait = (2 ** attempt) + random.uniform(0, 1)
            await asyncio.sleep(wait)
        except ValidationError:
            raise  # Never retry validation errors
    raise MaxRetriesExceeded(f"Failed after {max_retries} attempts")
```

**Idempotency Keys**
- Generate idempotency key = hash(tool_name + args + request_id)
- Store result in cache with key; on retry, return cached result
- Prevents duplicate side effects (double payments, duplicate emails)

---

## Reliability Patterns

### Circuit Breaker
```
State: CLOSED → (N failures in window) → OPEN → (timeout) → HALF_OPEN
CLOSED: pass through calls normally
OPEN: fail immediately (no downstream calls)
HALF_OPEN: allow one test call; success → CLOSED, failure → OPEN
```
- Prevents cascade failure when downstream tool is degraded
- Thresholds: 5 failures in 30s window → open; 30s timeout before half-open

### Fallback Hierarchy
```
Primary tool → Fallback tool → Cached result → Graceful degradation
Example: web_search_primary → web_search_backup → cached_search_result → "Unable to retrieve current information"
```

### Human-in-the-Loop (HITL) Triggers
**Escalate to human when**:
- Confidence score < threshold (domain-specific: 60-95%)
- Financial transaction > $X threshold
- Irreversible action with significant impact (delete, publish, send)
- Multiple consecutive tool failures (>3)
- Agent detects ambiguity it cannot resolve
- Task scope expansion beyond original authorization

**HITL Implementation in LangGraph**
```python
from langgraph.graph import StateGraph
from langgraph.checkpoint.postgres import PostgresSaver

graph = StateGraph(AgentState)
# ... define nodes ...

# Interrupt before taking irreversible action
graph.add_node("confirm_action", confirm_action_node)
checkpointer = PostgresSaver.from_conn_string(DB_URI)
app = graph.compile(checkpointer=checkpointer, interrupt_before=["confirm_action"])

# Resume after human approval
app.invoke(None, config={"configurable": {"thread_id": thread_id}})
```

### Confidence Thresholds by Domain
| Domain | Escalate Below |
|--------|--------------|
| General information | 60% |
| Medical/legal advice | 95% |
| Financial transactions | 85% |
| Code deployment | 80% |
| Customer communication | 75% |

---

## LangGraph — Production State Machines

### Core Concepts
- **StateGraph**: nodes (functions) + edges (transitions) + state (shared TypedDict)
- **Checkpointing**: automatic state save at each step; enables pause/resume
- **Time-travel**: replay from any past checkpoint for debugging
- **Interrupts**: pause execution before/after specific nodes for HITL

**State Definition**
```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]  # append-only message list
    task: str
    tool_results: list
    iterations: int
    final_answer: str | None
```

**Graph Definition Pattern**
```python
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode

def should_continue(state: AgentState) -> str:
    last_message = state["messages"][-1]
    if last_message.tool_calls:
        return "tools"  # route to tool execution
    return "end"  # task complete

builder = StateGraph(AgentState)
builder.add_node("agent", agent_node)
builder.add_node("tools", ToolNode(tools))
builder.set_entry_point("agent")
builder.add_conditional_edges("agent", should_continue, {"tools": "tools", "end": END})
builder.add_edge("tools", "agent")  # tools → back to agent

app = builder.compile(checkpointer=PostgresSaver.from_conn_string(DB_URI))
```

**Checkpointing Backends**
- `SqliteSaver`: development only; single-process
- `PostgresSaver`: production; concurrent access; recommended
- `RedisSaver`: high-throughput; ephemeral (no long-term storage)
- `AsyncPostgresSaver`: async PostgreSQL; required for async applications

---

## Production Deployment

### Async Queue Architecture
**Celery + Redis (Python standard)**
```python
from celery import Celery

app = Celery('agents', broker='redis://localhost:6379/0', backend='redis://localhost:6379/1')

@app.task(bind=True, max_retries=3, default_retry_delay=60)
def run_agent_task(self, task_id: str, task_data: dict):
    try:
        result = agent.run(task_data)
        return result
    except TransientError as exc:
        raise self.retry(exc=exc, countdown=2 ** self.request.retries)
```

**Temporal (Durable Execution)**
- Workflows survive process crashes, server restarts
- Activities = individual tool calls or agent steps
- Automatic retry with configurable policies
- **When to use**: tasks > 30 seconds, multi-step with non-idempotent side effects, cross-system coordination
```python
from temporalio import workflow, activity

@activity.defn
async def call_tool(tool_name: str, args: dict) -> dict:
    # Tool execution; Temporal handles retries, timeouts
    return await tool_registry[tool_name].execute(args)

@workflow.defn
class AgentWorkflow:
    @workflow.run
    async def run(self, task: AgentTask) -> str:
        for step in task.steps:
            result = await workflow.execute_activity(
                call_tool, step.tool, step.args,
                schedule_to_close_timeout=timedelta(minutes=5)
            )
```

### Framework Performance Comparison

| Framework | Task Completion Rate | Avg Cost/Query | Best For |
|-----------|---------------------|----------------|----------|
| LangGraph | 76% TCR | $0.18 | Complex workflows, production |
| CrewAI | 71% TCR | $0.22 | Role-based multi-agent |
| AutoGen/AG2 | 68% TCR | $0.35 | Research/code tasks |
| OpenAI Agents SDK | 74% TCR | $0.20 | OpenAI-native workloads |
| Claude Agent SDK | 77% TCR | $0.19 | Anthropic-native, best reasoning |

---

## Evaluation Framework

### Three-Layer Evaluation

**Layer 1: Pre-Deployment (Offline)**
- Unit tests: individual tool calls, specific sub-tasks
- Integration tests: end-to-end task completion on golden dataset
- Red-teaming: adversarial inputs, prompt injection attempts
- Benchmarks: task-specific datasets (SWE-bench for code, WebArena for web tasks)

**Layer 2: Simulation (Shadow Mode)**
- Run new agent version on production traffic without affecting users
- Compare outputs with current version
- A/B testing framework with statistical significance

**Layer 3: Production Monitoring**
- Task Completion Rate (TCR): % of tasks completed without error/escalation
  - Benchmark: >85% TCR before production deployment
- Hallucination Rate: factual errors in outputs
  - Legal/medical RAG: 17-33% without mitigation; <5% target with Self-RAG + reranking
- Cost per task: total LLM + tool costs per successful task completion
- P95 latency: tail latency for user experience
- Escalation rate: % tasks requiring HITL (target <10% at steady state)

**LLM-as-Judge Evaluation**
```python
def evaluate_agent_output(task, output, reference=None):
    judge_prompt = f"""
    Task: {task}
    Agent Output: {output}
    {"Reference: " + reference if reference else ""}
    
    Evaluate on:
    1. Task completion (0-10): Did the agent accomplish the task?
    2. Accuracy (0-10): Is the information correct?
    3. Efficiency (0-10): Was the approach efficient (minimal unnecessary steps)?
    
    Return JSON: {{"completion": X, "accuracy": X, "efficiency": X, "reasoning": "..."}}
    """
    # Use different model family for evaluation to avoid circular bias
    return evaluate_with_gpt5(judge_prompt)
```

---

## Cost Optimization

### Prompt Caching
- Anthropic prompt caching: 90% cost reduction on static context
- Cache system prompts, tool definitions, reference documents
- Implementation: `cache_control: {"type": "ephemeral"}` on message blocks
- Cache TTL: 5 minutes; longer available for extended caching

### Model Routing
- 60-80% total cost savings with quality parity
- Route decision: complexity classifier → model selection
- Simple queries → Haiku ($0.25/M); medium → Sonnet ($3/M); complex → Opus ($15/M)
- RouteLLM: open-source classifier-based router

### Trajectory Compression
- After each major step, summarize prior context into compact representation
- 40-60% token reduction in long-running agents
- LangGraph built-in: use `summarize_messages` as node before context limit

### Tool Call Optimization
- Batch multiple tool calls when APIs support it
- Parallel tool calls: Claude/GPT support concurrent tool execution
- Cache tool results: Redis TTL based on data freshness requirements
- ReWOO for predictable sequences: 5x fewer LLM calls

---

## Claude-Specific Agentic Capabilities

### Minimal Footprint Principle
- Request only necessary permissions
- Prefer reversible over irreversible actions
- Confirm before taking high-impact actions
- Don't store sensitive information beyond immediate need

### Tool Search Tool
- Claude can query available tools dynamically (not just static tool list)
- Critical for large tool registries (100+ tools) where all tool schemas won't fit in context
- Use deferred tool loading + ToolSearch for large MCP servers

### Computer Use API
- Control desktop environments via mouse, keyboard, screenshot
- Coordinate-based clicking; text input; key combinations
- Error-prone; requires vision feedback loop
- Best for: form filling, legacy desktop app automation, browser automation where no API exists

### Extended Thinking with Interleaved Tool Calls
- Enable thinking before AND between tool calls
- Highest accuracy for complex multi-step reasoning
- Cost: ~3-5x standard response
- Use for: strategic planning, complex debugging, high-stakes decisions

---

## Known Failure Incidents and Lessons

### Replit Agent DROP DATABASE (2024)
- Agent given broad database access
- Agent executed DROP DATABASE in production
- **Lesson**: never give production database write access directly; use intermediary with validation layer; require explicit confirmation for destructive DDL operations

### Claude Code Recursion Loop (2024)
- Claude Code recursion loop consumed 1.67B tokens in 5 hours
- No circuit breaker on token consumption
- **Lesson**: implement hard token budget limits with circuit breakers; alert on >10x expected cost per task; Temporal timeout = mandatory for all production agents

### Key Safety Patterns
1. **Token budget limits**: hard cap per task execution (warn at 80%, stop at 100%)
2. **Action confirmation gates**: irreversible actions require explicit approval
3. **Scope escalation detection**: agent attempting to access resources beyond task scope → immediate stop
4. **Audit log everything**: every tool call, parameter, result logged for forensics
5. **Separate test/prod credentials**: agent should never have production DB credentials in dev

---

## Expert Calibration Notes

- **Most "agentic" problems are routing problems**: before building a 5-agent system, verify a single agent with good tools can't solve it
- **Reliability degrades multiplicatively in chains**: if each step is 90% reliable, a 5-step chain is 0.9^5 = 59% reliable — reliability engineering is the central challenge
- **Checkpointing is not optional in production**: any agent running > 30 seconds needs durable state storage for resume after failure
- **HITL thresholds must be domain-calibrated**: generic confidence thresholds fail; calibrate on your specific task and domain
- **LangGraph won the framework war for production** (as of 2026): 76% of production agentic deployments; strong Anthropic integration, graph visualization, checkpointing built-in
- **The goal is not full autonomy**: optimal agentic systems know when to act and when to ask — overtly autonomous agents that never escalate are a liability, not a feature
