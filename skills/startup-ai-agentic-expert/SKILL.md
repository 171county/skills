---
name: startup-ai-agentic-expert
description: Expert-level knowledge for building production AI agentic systems at startups. Covers agentic architecture (ReAct, Plan-and-Execute, ToT), agent frameworks (LangGraph, CrewAI, AutoGen, OpenAI Agents SDK), multi-agent orchestration, memory systems (pgvector, Pinecone, Mem0), tool integration (MCP, browser-use, code execution), LLM selection and cost optimization, reliability engineering (HITL, guardrails, hallucination mitigation), observability (LangSmith, Langfuse, Arize Phoenix), and deployment infrastructure (Temporal, async queues, Kubernetes). Use when designing, building, debugging, or scaling AI agents or agentic products at a startup.
---

# Startup AI Agentic Expert

## Core Mental Model

An agentic system is an LLM acting as a **reasoning engine inside a loop** — observe, think, plan, act, repeat until goal achieved. This is fundamentally different from a prompt-response pattern.

```
OBSERVE → THINK → PLAN → ACT → OBSERVE (repeat)
```

**The key insight for 2025:** Most production value comes from **deterministic workflows with LLM nodes** — not open-loop autonomous agents. If you can draw the flowchart in advance, use a workflow. If every decision requires an LLM, use an agent.

---

## 1. Architecture Fundamentals

### The ReAct Pattern (Foundation of All Agents)

ReAct interleaves **reasoning traces** with **action calls**, incorporating observations:

```
Thought: I need to find the current stock price of AAPL.
Action: search_web("AAPL stock price today")
Observation: AAPL is trading at $189.42 as of 2:30pm ET.
Thought: Now I can answer the user's question.
Final Answer: AAPL is trading at $189.42.
```

**Production ceiling:** Beyond 8 reasoning steps, accumulated context degrades LLM performance. Design tasks with this constraint.

### Planning Approaches

| Style | Description | Best For |
|---|---|---|
| **ReAct** | Interleaved thought+action | Most tasks; 3–8 steps |
| **Plan-and-Execute** | Separate planner + executor | Long-horizon, stable structure |
| **Tree of Thoughts** | Multiple reasoning branches | High-stakes decisions |
| **Reflexion** | Agent self-critiques, retries | Iterative improvement tasks |

### Function Calling (Anthropic Claude)

```python
tools = [{
    "name": "get_weather",
    "description": "Get current weather for a location",
    "input_schema": {
        "type": "object",
        "properties": {
            "location": {"type": "string", "description": "City, state"}
        },
        "required": ["location"]
    }
}]

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in NYC?"}]
)

if response.stop_reason == "tool_use":
    tool_call = next(b for b in response.content if b.type == "tool_use")
    result = execute_tool(tool_call.name, tool_call.input)
    # Feed result back as tool_result message
```

**Tool-use reliability scores (2025):** Anthropic 8.4/10, Google 7.9/10, OpenAI 6.3/10.

### Agent Exit Conditions (Always Define These)

Every production agent must have explicit termination:
1. **Success**: goal achieved, final answer returned
2. **Max steps**: hard limit (typically 10–25 steps)
3. **Confidence threshold**: LLM declares uncertainty
4. **Human interrupt**: HITL checkpoint triggered
5. **Error state**: tool failure > N retries
6. **Time limit**: wall clock exceeded

Missing termination conditions = infinite loops = #1 production failure mode.

---

## 2. Framework Selection

### Decision Tree

```
Already in LangChain ecosystem?
├── YES → LangGraph (stateful, production-grade)
└── NO  → Need multi-agent with roles?
          ├── YES, simple roles → CrewAI (fast to prototype)
          ├── YES, complex state → LangGraph
          └── NO, single agent → OpenAI Agents SDK or Claude SDK
```

### Framework Comparison

| Framework | Production Maturity | Best For | Avoid When |
|---|---|---|---|
| **LangGraph** | ★★★★★ | Complex stateful workflows, HITL | Simple single-agent tasks |
| **CrewAI** | ★★★★☆ | Rapid prototyping, multi-role | Fine-grained control needed |
| **AutoGen / AG2** | ★★★★☆ | Research, coding, group chat | Deterministic production flows |
| **OpenAI Agents SDK** | ★★★★☆ | Simple handoffs, OpenAI-native | Multi-provider or complex state |
| **LlamaIndex** | ★★★★☆ | Knowledge-intensive retrieval agents | Pure orchestration without RAG |

### LangGraph (Recommended for Production)

Models agents as **directed graphs**: nodes = Python functions, edges = transitions, state = shared TypedDict.

```python
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]  # Append-only reducer
    final_answer: str

def agent_node(state: AgentState):
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

def tool_node(state: AgentState):
    results = execute_tools(state["messages"][-1].tool_calls)
    return {"messages": results}

def should_continue(state: AgentState):
    last_msg = state["messages"][-1]
    return "tools" if last_msg.tool_calls else END

workflow = StateGraph(AgentState)
workflow.add_node("agent", agent_node)
workflow.add_node("tools", tool_node)
workflow.set_entry_point("agent")
workflow.add_conditional_edges("agent", should_continue)
workflow.add_edge("tools", "agent")

app = workflow.compile(checkpointer=MemorySaver())
```

**Checkpointer selection:**
- `MemorySaver`: development only
- `SqliteSaver`: single-server production
- `PostgresSaver`: multi-instance, production-grade

**Key advantages:** built-in checkpointing, time travel (replay from any checkpoint), native HITL via `interrupt_before`/`interrupt_after`, streaming intermediate state.

**State design rule:** Use `Annotated[list, operator.add]` for append-only fields — prevents silent overwrites. Design state schema first; it's the most consequential LangGraph decision.

### CrewAI (Fastest to First Working Agent)

```python
from crewai import Agent, Task, Crew, Process

researcher = Agent(
    role="Research Analyst",
    goal="Find accurate information about {topic}",
    backstory="You are an expert researcher...",
    tools=[search_tool, web_scrape_tool],
    llm="claude-sonnet-4-6"
)

writer = Agent(role="Content Writer", goal="Transform research into content",
               backstory="You craft compelling narratives...", llm="gpt-4o")

research_task = Task(
    description="Research the latest developments in {topic}",
    expected_output="A comprehensive research report with sources",
    agent=researcher
)

writing_task = Task(
    description="Write an article based on the research",
    expected_output="A 500-word article",
    agent=writer,
    context=[research_task]  # Receives researcher output
)

crew = Crew(agents=[researcher, writer], tasks=[research_task, writing_task],
            process=Process.sequential)

result = crew.kickoff(inputs={"topic": "AI agents in 2025"})
```

**Production caution:** Hierarchical mode adds 30–50% extra tokens. Monitor `crew.usage_metrics`. Implement task output truncation for long chains.

---

## 3. Multi-Agent Orchestration Patterns

### Core Patterns

**1. Supervisor/Worker (Hierarchical)**
```
[Supervisor Agent]
    ├── [Research Worker]
    ├── [Code Worker]
    └── [Review Worker]
```
Supervisor decomposes goal, delegates, synthesizes. Best for tasks with clearly separable subtasks.

**2. Parallel Fan-Out/Fan-In**
```python
import asyncio

async def parallel_research(topic):
    tasks = [
        agent_a.ainvoke({"query": f"{topic} - technical"}),
        agent_b.ainvoke({"query": f"{topic} - business"}),
        agent_c.ainvoke({"query": f"{topic} - market"})
    ]
    results = await asyncio.gather(*tasks)
    return synthesizer.invoke({"inputs": results})
```

**3. Pipeline (Sequential)** — Simple but brittle; errors compound. Validate outputs between stages.

### When to Add Multi-Agent

**Don't.** Add only when:
1. Single agent demonstrably can't handle task breadth
2. Parallelization clearly reduces latency in production
3. Specialization provides measurable quality improvement

Most production value comes from 1–2 agent systems. 3+ agent chains add failure modes faster than capability.

### Anti-Patterns

- **Token explosion**: manager mode adds 30–50% tokens — budget for this
- **Silent failure propagation**: worker failures must surface to supervisor
- **Circular delegation**: add cycle detection; Agent A → B → A is an infinite loop
- **Over-delegation**: don't use hierarchical for tasks that sequential handles fine

---

## 4. Memory Systems

### The Four Memory Types

| Type | Storage | Description |
|---|---|---|
| **Working/In-Context** | Context window | Active messages, current task state |
| **Episodic** | Vector DB / RDBMS | Past interactions, user preferences |
| **Semantic** | Vector DB + KV | Facts, knowledge, documentation |
| **Procedural** | System prompt updates | Learned behaviors, how-to-behave |

### Vector Database Selection

| DB | Best For | Cost | Ops |
|---|---|---|---|
| **pgvector** | <5M vectors, existing PostgreSQL | Very low | Near-zero |
| **Pinecone** | Managed, fast time-to-value, >5M vectors | $50–$500/mo | Zero |
| **Qdrant** | Open-source, high performance, hybrid search | Low (self-hosted) | Moderate |
| **Weaviate** | Native hybrid search (semantic + BM25) | Moderate | Moderate |
| **Chroma** | Local dev, prototyping | Free | Zero |

**Startup recommendation:** Start with `pgvector` if you have PostgreSQL. Migrate to Qdrant or Pinecone when you exceed 5M vectors or need sub-10ms latency.

**Hybrid search is non-optional for production RAG** — semantic search alone misses exact keyword matches (product codes, names, IDs). Always combine vector + BM25.

### Long-Term Memory Implementation (Mem0)

```python
from mem0 import MemoryClient

memory = MemoryClient(api_key="...", user_id="user_123")

# Store after each interaction
memory.add(messages=[
    {"role": "user", "content": "I prefer bullet points"},
    {"role": "assistant", "content": "Noted! I'll use bullet points."}
])

# Retrieve before each agent run
relevant_memories = memory.search(query="user formatting preferences", limit=5)
context = "\n".join([m["memory"] for m in relevant_memories])
system_prompt = f"User preferences:\n{context}\n\nYou are a helpful assistant."
```

**Mem0 benchmarks:** 91% lower p95 latency, 90% token reduction vs. full-context prompting.

**Context window management rules:**
- Long contexts degrade attention after ~32K tokens in practice (even with 200K windows)
- Compress aggressively: summarize conversation history, truncate tool outputs
- Use structured data, not prose, in context

---

## 5. Tool Integration

### Tool Design Principles

Poorly defined tool schemas are the #1 cause of irrelevant API invocations, expanding context and cost (Amazon engineering finding).

```python
tools = [{
    "name": "search_products",      # Specific verb_noun naming
    "description": (
        "Search the product catalog for items matching a query. "
        "Use when the user asks about product availability, pricing, or specs. "
        "Do NOT use for order status — use get_order_status instead."  # Negative examples reduce confusion
    ),
    "input_schema": {
        "type": "object",
        "properties": {
            "query": {
                "type": "string",
                "description": "Natural language search query, e.g., 'blue running shoes size 10'"
            },
            "max_results": {"type": "integer", "default": 5, "description": "1-20"}
        },
        "required": ["query"]
    }
}]
```

### Tool Categories

| Category | Options | Notes |
|---|---|---|
| **Web Search** | Tavily ($0.001/query), Brave Search (free tier), Exa.ai | Tavily purpose-built for LLM agents |
| **Code Execution** | e2b.dev (managed sandboxes), Docker + AutoGen, Modal | e2b: $0.000225/cpu-second |
| **Browser Automation** | Browser-Use (89.1% WebVoyager), Playwright+Vision, Stagehand | Browser-Use open-source, best benchmark |
| **Database** | Direct SQL, pgvector queries, Supabase | Always parameterize queries |
| **APIs** | Any REST/GraphQL wrapped in tool schema | Validate outputs before returning to agent |

### MCP (Model Context Protocol)

Anthropic's open standard (Nov 2024) — write a tool once, use it across Claude, GPT-4, Gemini, Cursor, VS Code. **97M+ monthly SDK downloads** as of late 2025.

```python
from mcp import Server

server = Server("my-tools")

@server.tool()
async def query_database(sql: str) -> str:
    """Execute a read-only SQL query against the analytics database."""
    result = await db.execute(sql)
    return result.to_json()

# Any Claude/GPT/Gemini agent can use this via mcp_servers config
```

---

## 6. LLM Selection for Agents

### Model Comparison (2025–2026)

| Model | Context | Input $/1M | Output $/1M | Tool Use Score | Best For |
|---|---|---|---|---|---|
| **Claude Sonnet 4.6** | 200K | $3.00 | $15.00 | 8.4/10 | Production agents, coding |
| **Claude Opus 4.8** | 200K | ~$15 | ~$75 | 8.4/10 | Complex reasoning, high-stakes |
| **GPT-4.1** | 1M | $2.00 | $8.00 | 6.3/10 | Large context, general purpose |
| **Gemini 2.5 Pro** | 1M | $1.25 | $5.00 | 7.9/10 | Long-context, multimodal |
| **Gemini 2.5 Flash** | 1M | $0.30 | $2.50 | 7.0/10 | High-volume, cost-sensitive |
| **Llama 4 Scout** | 10M | $0.11 | $0.34 | 6.0/10 | Open-source, private deployment |

### Selection Framework

```
1. Eliminate non-starters:
   - Privacy/compliance required? → Llama 4 (self-hosted) or private cloud endpoints
   - Budget < $0.50/1M tokens? → Gemini 2.5 Flash or Llama 4
   - Context > 200K tokens? → GPT-4.1, Gemini 2.5, or Llama 4

2. Match to intelligence tier:
   - Simple routing/classification → Haiku, Gemini Flash, GPT-4o-mini
   - Standard tool use + reasoning → Claude Sonnet 4.6, GPT-4.1
   - Complex multi-step, high stakes → Claude Opus 4.8, Gemini 2.5 Pro

3. Agent-specific considerations:
   - Tool reliability critical → Claude (8.4/10)
   - Fastest latency → GPT-4o
   - Long conversation memory → Gemini 2.5 Pro (1M context)
   - Audit/explainability → Claude (best chain-of-thought transparency)
```

### Cost Management

**Token multipliers in agentic systems:**
- ReAct loop (10 steps): ~10× a single call's input tokens (context accumulates)
- Hierarchical CrewAI (manager mode): +30–50% tokens
- RAG retrieval: +2–5K tokens per tool call

**Cost optimization tactics:**
1. Route cheap steps to cheap models (classify → Haiku, reason → Sonnet)
2. Compress tool outputs — extract only relevant fields, not raw API responses
3. Cache aggressively — exact match cache for common queries (Helicone, Redis)
4. **Prompt caching** (Anthropic): repeated system prompts cost 10% after first call
5. Use Anthropic Batch API for non-real-time tasks (50% cost reduction)

**Typical production costs:**
- Simple tasks, 5–10 steps, Sonnet: **$0.05–$0.50 per run**
- Complex tasks, 20–30 steps, Opus: **$0.50–$5.00 per run**
- Deep research + browser automation, 50+ steps: **$5.00–$50.00 per run**

---

## 7. Reliability & Safety

### Top Failure Modes

| Failure Mode | Root Cause | Fix |
|---|---|---|
| **Infinite loop** | No termination condition | Hard step limit (10–25), time limit |
| **Context explosion** | Unchecked tool output accumulation | Summarize history at intervals; truncate outputs |
| **Hallucinated tool calls** | Vague tool descriptions | Precise schemas with negative examples |
| **Silent failures** | No output validation | Schema validation on every tool output |
| **Error propagation** | Agent treats error as result | Explicit error handling; retry with backoff |
| **Prompt injection** | Malicious content in tool results | Sanitize tool outputs; separate trusted/untrusted |

### Human-in-the-Loop (HITL)

**When to interrupt:**
- Action is **irreversible** (send email, delete file, charge payment)
- Confidence score below threshold
- Action cost exceeds budget limit
- Novel situation detected

**LangGraph HITL:**
```python
app = workflow.compile(
    checkpointer=MemorySaver(),
    interrupt_before=["execute_payment", "send_email", "delete_file"]
)

# Run until interrupt
thread_id = {"configurable": {"thread_id": "session_123"}}
for chunk in app.stream({"messages": [user_message]}, thread_id):
    if "__interrupt__" in chunk:
        show_approval_ui(chunk["__interrupt__"])

# After human approves:
app.update_state(thread_id, {"approved": True})
for chunk in app.stream(None, thread_id):  # Resume
    process_output(chunk)
```

### Guardrails

```python
from pydantic import BaseModel, validator
from typing import Literal

class AgentOutput(BaseModel):
    action: Literal["search", "summarize", "draft_email", "escalate"]
    content: str
    confidence: float
    requires_human_review: bool

    @validator("confidence")
    def validate_confidence(cls, v, values):
        if v < 0.7 and not values.get("requires_human_review"):
            raise ValueError("Low confidence outputs must require human review")
        return v
```

**Guardrail layers:** (1) input sanitization, (2) tool argument validation, (3) output schema validation, (4) reversibility check before irreversible actions.

### Hallucination Mitigation

1. **RAG grounding** with source citations — agent must quote sources
2. **Structured outputs** — JSON schema constraints dramatically reduce hallucinations
3. **Self-consistency sampling** — run 3 generations, use majority vote for critical facts
4. **Confidence gating** — uncertainty triggers human escalation

Fallback mechanisms reduce hallucination errors by **96%** when uncertainty thresholds trigger human handoffs.

---

## 8. Observability & Evals

### Tool Comparison

| Tool | Best For | Self-Hostable | Cost |
|---|---|---|---|
| **LangSmith** | LangGraph-native teams | Yes (BYOC) | Free tier + $39/mo |
| **Langfuse** | Data sovereignty, open-source | Yes (MIT) | Free self-host |
| **Arize Phoenix** | RAG evaluation, model monitoring | Yes | Free + paid |
| **Helicone** | Simple integration, caching | No | Free tier |

**Recommended stack:**
- Langfuse for tracing (open-source, self-hostable)
- Arize Phoenix for RAG evaluation
- W&B for experiment tracking during development

### What Every Agent Trace Must Capture

```
Session
├── Input (user message)
├── Chain
│   ├── LLM Call (prompt, response, tokens, latency, cost)
│   ├── Tool Call (name, args, result, latency)
│   └── ... (repeat)
├── Output (final response)
└── Metadata (user_id, session_id, model, total_cost, total_latency)
```

### Evaluation Types

| Type | When | What It Checks |
|---|---|---|
| **Unit evals** | CI/CD per commit | Individual node behavior |
| **Integration evals** | Pre-deploy | End-to-end trajectories |
| **LLM-as-judge** | Automated at scale | Quality, helpfulness, accuracy |
| **Human evals** | Periodic sampling | Ground truth calibration |
| **Online evals** | Production | Live regression detection |

**LLM-as-judge:**
```python
from langsmith import evaluate

def helpfulness_evaluator(run, example):
    score = llm.invoke(f"""
    Rate 1-10 for helpfulness:
    User: {example.inputs['user_message']}
    Agent: {run.outputs['response']}
    Return JSON: {{"score": <1-10>, "reasoning": "..."}}
    """)
    return {"score": score["score"] / 10}

evaluate(my_agent, data="my-eval-dataset",
         evaluators=[helpfulness_evaluator], experiment_prefix="v2-agent")
```

**Warning:** 37% gap between lab benchmarks and real-world performance. Build your own domain-specific eval set from real user interactions — don't rely on MMLU or SWE-bench.

---

## 9. Deployment & Infrastructure

### The Core Challenge

Agents are long-running and non-deterministic. Traditional serverless (Lambda) has 15-minute timeouts and no mid-run observability. Solutions:

### Durable Execution: Temporal

Best for production agents running > 15 minutes.

```python
from temporalio import activity, workflow
from datetime import timedelta

@activity.defn
async def call_llm(prompt: str) -> str:
    """Non-deterministic side effect — retried independently"""
    response = await openai_client.chat.completions.create(...)
    return response.choices[0].message.content

@workflow.defn
class ResearchAgent:
    @workflow.run
    async def run(self, goal: str) -> str:
        for step in range(25):
            action = await workflow.execute_activity(
                call_llm, build_prompt(goal, self.history),
                schedule_to_close_timeout=timedelta(seconds=30)
            )
            if action.type == "final_answer":
                return action.content
            result = await workflow.execute_activity(
                execute_tool, action.tool, action.args,
                retry_policy=RetryPolicy(maximum_attempts=3)
            )
            self.history.append((action, result))
```

Used by OpenAI, Snap, Netflix, JPMorgan Chase, Stripe, Salesforce.

### Async Queue Architecture

For high-throughput agents:

```
[API Gateway] → [Task Queue (Redis/BullMQ)] → [Agent Workers (K8s pods)]
                                               ↓
                                         [Result Store (Redis/Postgres)]
                                               ↓
                                    [Webhook / SSE to client]
```

```python
from celery import Celery

app = Celery("agents", broker="redis://localhost:6379/0",
             backend="redis://localhost:6379/1")

@app.task(bind=True, max_retries=3, retry_backoff=True, retry_backoff_max=60)
def run_agent_task(self, session_id: str, user_message: str):
    try:
        result = agent.invoke({"messages": [user_message]})
        store_result(session_id, result)
        notify_client(session_id, result)
    except Exception as exc:
        raise self.retry(exc=exc, countdown=2 ** self.request.retries)
```

### Kubernetes Considerations

- **HPA**: scale workers based on queue depth
- **Pod disruption budgets**: graceful agent handoff during rolling deploys
- **Resource limits**: agents OOM from long contexts — set memory limits with headroom

---

## 10. Product Patterns & UX

### The Autonomy Spectrum

```
COPILOT                                    AUTONOMOUS
[Suggest] ←——————————————————————→ [Autopilot]
Human controls everything         Agent controls everything
Low trust, low stakes             High trust, high stakes
```

**Autonomy Slider UX pattern:**
- **Suggest**: agent proposes each action, human approves
- **Copilot**: agent handles routine, asks for important ones
- **Autopilot**: agent handles everything, reports results

### When Agents vs Workflows

**Use agents when:**
- Task requires adaptive decision-making (path unknown upfront)
- Multiple heterogeneous tools in non-fixed sequence
- Happy path covers < 70% of cases
- Requires iteration and self-correction

**Use workflows when:**
- Sequence of steps is fixed and known
- Need determinism and auditability
- Optimizing for speed and cost (3–5× cheaper per run than agents)

### UX Design Patterns

1. **Progressive Disclosure**: show abbreviated thinking by default, expandable for detail
2. **Action Preview**: "I'm about to send this email to john@example.com. Proceed?"
3. **Live Status**: stream intermediate steps; silence is anxiety-inducing
4. **Trust Calibration**: start supervised, progressively grant autonomy
5. **Undo/Rollback**: every consequential action should be reversible — if not, require pre-approval
6. **Transparent Uncertainty**: "I'm not confident about this. Verify with a source?"

### Real Production Examples (2025)

| Product | Agent Type | Result |
|---|---|---|
| Klarna AI Support | Customer service | 2.3M conversations/month; 11 min → 2 min resolution |
| GitHub Copilot Workspace | Code agent | Multi-file PR generation |
| JPMorgan AI Agents | Investment banking | 450+ use cases; 30-sec presentation generation |
| Cursor/Windsurf | IDE coding copilot | Most successful agentic products by ARR |

---

## 11. Startup Strategy

### MVP Architecture Phases

**Phase 1: Single Agent + 2–3 Tools (Weeks 1–4)**
```
User → [Single LLM Agent] → [Tool 1: Search]
                           → [Tool 2: Database]
                           → [Tool 3: Email/Slack]
```
Validate value before adding complexity. Most successful products never need multi-agent.

**Phase 2: Add Memory + Persistence (Weeks 4–8)**
Add pgvector for long-term memory and PostgresSaver for session continuity.

**Phase 3: Multi-Agent Only If Needed (Week 8+)**
Only when Phase 2 demonstrably can't handle breadth.

### Build vs Buy

**Buy when:** < 1M agent conversations/year, time-to-market is the constraint, standard use case.
**Build when:** custom business logic platforms can't express, data sovereignty required, > 1M conversations/year (unit economics flip), proprietary workflow is a competitive moat.

**Cost benchmarks:**
- Basic custom MVP (1 agent, 2–3 tools): $15K–$50K
- Production single-agent system: $50K–$100K
- Full multi-agent platform: $250K–$400K+

### Vendor Lock-in Mitigation

```python
# Abstraction layer — swap any component without touching business logic
class AgentStack:
    def __init__(self):
        self.llm = LiteLLM(model="claude-sonnet-4-6")   # Swap in config
        self.memory = MemoryInterface(backend="pgvector") # Swap in config
        self.tracer = LangfuseTracer()                    # Self-hostable
        self.tools = ToolRegistry()                       # MCP-compatible
```

---

## 12. What's Working vs. Hype (2025)

### Validated in Production

- Document processing & classification (40–70% deflection rates)
- Customer service augmentation (tier-1 query handling, Klarna model)
- Code assistance (Cursor, Copilot — most commercially successful agentic category)
- IT operations automation (ticket triage, alert classification)
- Data analysis workflows (agent reads data, writes code, executes, interprets)
- Content generation pipelines

### Still Hype / Not Production-Ready

- Fully autonomous open-ended task agents (production agents check in with humans 68% of the time at ≤10 steps)
- Multi-agent societies at scale (interesting research, fragile production)
- Long-horizon autonomous execution (days/weeks) — memory, reliability, cost all degrade
- Computer use for enterprise workflows (UIs change; agents break)
- Agent-generated code deployed directly to production without human review

### Key Statistic

**88% of early AI agent adopters report positive ROI on ≥1 use case** (Google Cloud 2025, 3,466 leaders). Average ROI: **171%**.

**But:** 40% of agentic AI projects will be canceled by 2027 (Gartner) — primary reason: reliability. Build evals before you scale.

---

## Reference Files

- [Architecture Deep Dive](./reference/architecture.md)
- [Framework Comparison Guide](./reference/frameworks.md)
- [Memory & RAG Patterns](./reference/memory-rag.md)
- [Reliability & Safety Playbook](./reference/reliability.md)
- [Deployment Infrastructure](./reference/deployment.md)
- [Cost Optimization Guide](./reference/cost-optimization.md)
