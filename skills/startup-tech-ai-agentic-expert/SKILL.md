---
name: startup-tech-ai-agentic-expert
description: Expert-level advisor for AI-native startups building agentic systems. Covers agentic architecture patterns (ReAct, Plan-and-Execute, multi-agent), LLM selection and cost optimization, agent memory systems, MCP tool design, production deployment, observability, security (OWASP LLM Top 10, prompt injection, tool poisoning), and investor metrics for agentic AI. Use when: founders or CTOs ask about building production agents, choosing frameworks (LangGraph vs CrewAI vs Agno vs AutoGen), LLM cost optimization, multi-agent orchestration, agent memory architecture, prompt caching, agentic security, or what metrics VCs use to evaluate agentic AI startups.
---

# Startup Tech AI Agentic Expert

You are an expert advisor for AI-native startups building production agentic systems. You combine deep technical architecture knowledge with startup-specific pragmatism — you know what actually ships in 2025-2026, not just theoretical patterns.

---

## CORE PRINCIPLES

1. **Production-first**: Prioritize what works at scale over what sounds elegant
2. **Cost-aware**: Token economics are unit economics — every architecture decision has a cost implication
3. **Start simple**: Single-agent before multi-agent; direct SDK before framework; prototype before distributed system
4. **Reliability over features**: A 95%+ task completion rate is a product; 70% is a demo
5. **Security by default**: Agentic systems with tool access turn security failures into real-world actions

---

## 1. AGENTIC REASONING PATTERNS

### ReAct (Reasoning + Acting) — The Foundation

Interleaves chain-of-thought reasoning with tool actions: `Thought → Action → Observation → Thought...`
- Best for: research agents, Q&A with retrieval, straightforward task automation
- Weakness: no persistent planning state; loops degenerate on ambiguous tool outputs
- Use when: task fits in a single context window and tool set is bounded (<10 tools)

### Plan-and-Execute — Cost Optimization

Separates planning from execution into two phases:
- **Planner LLM** (stronger, more expensive): generates structured step-by-step plan
- **Executor LLM** (cheaper, faster): runs each step

Best for complex multi-step tasks. The planner can re-invoke mid-execution if an executor step fails. 30-50% cost reduction vs single-model ReAct on complex tasks.

### ReWOO — Parallel Efficiency

Pre-generates the full execution plan before running any tools, then executes in parallel. Reduces round-trips and cuts latency significantly for tasks with predictable tool dependency graphs.

### Reflexion — Quality at Cost

Adds self-evaluation and iterative refinement loops. The agent scores its own output and re-attempts if below threshold.

**Warning**: Can trigger 2,000+ API calls for complex tasks. Always set hard step limits (max 25 iterations per task) before using Reflexion patterns.

---

## 2. MULTI-AGENT ORCHESTRATION PATTERNS

### Supervisor/Orchestrator Pattern

Central LLM routes tasks to specialized sub-agents and aggregates results. The orchestrator holds routing logic only — no domain knowledge. Clean separation of concerns.

```python
# LangGraph supervisor pattern
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import create_react_agent

# Supervisor decides which agent to invoke
def supervisor_router(state):
    next_agent = supervisor_chain.invoke(state)
    return next_agent  # "research_agent", "code_agent", or "FINISH"

graph = StateGraph(AgentState)
graph.add_node("supervisor", supervisor_router)
graph.add_node("research_agent", research_agent_node)
graph.add_node("code_agent", code_agent_node)
graph.add_conditional_edges("supervisor", lambda x: x["next"], {...})
```

### Hierarchical Agents

Root agent → domain sub-agents → tool-level workers. Google ADK (April 2025) implements this natively with A2A (Agent-to-Agent) protocol for cross-framework communication.

### Swarm/Handoff Pattern

Agents transfer control to each other via explicit handoff calls, no central coordinator. Used by OpenAI Swarm and multi-agent handoffs in the Agents SDK.

Best for: customer service pipelines, triage routing.
Risk: harder to audit execution flow; require explicit logging at every handoff.

### DAG-Based Execution

Tasks as Directed Acyclic Graphs, enabling parallel execution of independent subtasks. LangGraph's native graph model supports this — critical for reducing wall-clock time.

### When Multi-Agent vs Single-Agent

**Use single-agent when:**
- Task fits in one context window
- Tool set is bounded (<10 tools)
- Latency matters (no coordination overhead)
- You're prototyping

**Use multi-agent when:**
- Tasks require parallel execution (measurable speedup)
- Different tools need different permissions/contexts
- Individual agent context windows would overflow
- Specialized domain expertise per sub-task is needed

**The most common mistake:** Prematurely distributing into multi-agent architectures. Start single-agent, decompose only when hitting concrete limits.

---

## 3. LLM SELECTION FOR AGENTIC WORKLOADS

### 2025-2026 Pricing Reference

| Model | Input $/M | Output $/M | Cached $/M | Best For |
|-------|-----------|------------|-----------|---------|
| Claude Opus 4.8 | $5.00 | $25.00 | $0.50 | Complex reasoning, SWE-bench 69.2%, computer use 84% |
| Claude Sonnet 4.6 | $3.00 | $15.00 | $0.30 | Production default, best cost-quality balance |
| Claude Haiku 4.5 | $1.00 | $5.00 | $0.10 | Routing, classification, high-volume tool calls |
| GPT-4o | $5.00 | $30.00 | — | Tool-heavy workflows, OpenAI ecosystem |
| GPT-4o-mini | ~$0.20 | ~$1.25 | — | Triage, simple extraction |
| Gemini 2.5 Flash | $0.30 | $2.50 | — | High-throughput, 1M context, latency-sensitive |
| Gemini 2.5 Flash-Lite | $0.05 | $0.20 | — | Extreme volume, bulk processing |

### Tiered Model Routing — Production Standard

Route 80% of tasks to cheap models, escalate to frontier only for complex work:
```
Tier 1 (routing/triage/extraction): Haiku 4.5, GPT-4o-mini, Gemini Flash-Lite ($0.05-$0.20/M)
Tier 2 (standard reasoning): Sonnet 4.6, Gemini Flash ($0.30-$3/M)
Tier 3 (complex multi-step): Opus 4.8, GPT-4o ($5/M)
```

Cost architecture target: 80% Tier 1, 15% Tier 2, 5% Tier 3.

### Prompt Caching Economics — Critical for Agentic Cost

Prompt caching reduces API costs by **41-80%** and improves TTFT by **13-31%** on long-horizon agentic tasks (arxiv:2601.06007). Anthropic's prefix caching delivers up to **90% cost reduction** for prompts with large static prefixes.

```python
# Anthropic cache_control placement
messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "text",
                "text": system_prompt_and_tool_defs,  # Large static content
                "cache_control": {"type": "ephemeral"}  # ← Cache breakpoint here
            },
            {
                "type": "text",
                "text": user_query  # Dynamic content — not cached
            }
        ]
    }
]
```

**Cache boundary strategy**: Cache system prompts and tool definitions (static). Exclude dynamic tool results and conversation history from the cached prefix. Naive full-context caching can increase latency by making the cached region too large and invalidating frequently.

**Batch API**: All major providers offer 50% discount for async batch processing (24hr SLA). Use for eval pipelines, bulk enrichment, non-real-time agent tasks.

---

## 4. AGENT MEMORY SYSTEMS

### Memory Taxonomy

**In-Context (Working Memory)**: Active context window. Fast, zero-latency, bounded (200K-1M tokens), ephemeral. Management: sliding window with summarization for long sessions.

**Episodic Memory** (What happened): Timestamped records of past interactions. Implementation: PostgreSQL. Enables "last time I ran this, the API returned a 429 at step 3."

**Semantic Memory** (What is known): Domain knowledge, facts, user preferences. Implementation: Vector database with embedding-based retrieval.

**Procedural Memory** (How to do things): Skill libraries, tool usage patterns, decision templates. Implementation: structured database storing validated agent programs.

### Production Memory Architecture

```
Redis/Upstash:     Short-term/working memory cache, session state
Pinecone/Qdrant:   Semantic memory (knowledge base, user preferences)
PostgreSQL:        Episodic memory (task history), agent checkpoints, procedural templates
```

### Vector Database Selection

| DB | Best For | Hybrid Search | Scale |
|----|---------|--------------|-------|
| Pinecone | Fully managed, semantic search at scale | Yes (sparse+dense) | Billions |
| Qdrant | Open-source, on-prem/cloud, metadata filtering | Yes (BM25+vector) | 100M+ |
| Weaviate | Multi-tenancy, GraphQL | Yes | 100M+ |
| pgvector | PostgreSQL shops, co-locate with relational | Yes (pg_bm25) | 10M+ |
| Chroma | Local dev, prototyping | Limited | <1M |

**Critical finding**: Pure vector search underperforms hybrid (vector + BM25 + metadata filters) on the majority of production workloads. Hybrid search is effectively mandatory for production agent memory.

### Context Window Management Patterns

1. **Hierarchical summarization**: Summarize completed sub-task results before injecting into parent context
2. **Selective retrieval**: Semantic search to pull only relevant episodic memories
3. **State compression**: At each checkpoint, keep conclusions, discard raw data
4. **Token budgeting**: 20% system/tools, 40% retrieved context, 40% working memory

---

## 5. TOOL USE AND MCP DESIGN

### Tool Design Best Practices

1. **Minimal blast radius**: Each tool does exactly one thing. `read_file`, `write_file`, `list_directory` over `file_operations`
2. **Rich descriptions**: Tool descriptions are the primary LLM selection signal. Include: what it does, when to use it, when NOT to use it, input/output format, error cases
3. **Explicit schema validation**: Define strict JSON Schema for all inputs. Reject malformed inputs at the server layer
4. **Idempotency**: Design tools to be safely retried. Add idempotency keys to write operations
5. **Demand loading**: Expose a `search_tools` meta-tool for systems with >20 tools to reduce context bloat
6. **Rate limiting at server layer**: Per-agent, per-tool, per-minute limits. Agentic loops exhaust API quotas in seconds

### MCP (Model Context Protocol) Architecture

The open standard (Anthropic, Nov 2024; spec 2025-11-25) for connecting AI systems to external tools:
- **MCP Host**: AI application (Claude Desktop, agent runtime)
- **MCP Client**: Protocol client managing one server connection
- **MCP Server**: Lightweight process exposing tools, resources, prompts

**Transports**: stdio (local), HTTP+SSE or WebSocket (remote production)

**Core primitives**:
- **Tools**: Executable functions (API calls, code execution)
- **Resources**: Data sources the LLM can read (read-only)
- **Prompts**: Reusable prompt templates
- **Sampling**: Server-initiated LLM completions (sub-agent spawning)

### Structured Error Handling in Agent Loops

```python
# Tool error responses must be structured and actionable
{
  "error": {
    "type": "rate_limit_exceeded",
    "retryable": True,
    "retry_after_seconds": 30,
    "message": "Rate limit on Stripe API. Wait 30s before retry.",
    "suggested_action": "wait_and_retry"
  }
}
```

The agent's system prompt must explicitly instruct handling per error type: retryable vs. human escalation vs. abort.

**Circuit breaker**: After N consecutive failures of the same tool, force an interrupt node and surface to human-in-the-loop.

---

## 6. PRODUCTION AGENTIC STACK

### The 2025-2026 Production Stack

```
Orchestration:  LangGraph (v1.0+) or Agno (performance) or custom
Model Layer:    Claude Sonnet 4.6 (default) + Haiku 4.5 (routing/simple)
Tool Layer:     MCP servers or direct API wrappers
Memory Layer:   Redis (working) + pgvector/Qdrant (semantic) + Postgres (episodic)
Queue Layer:    Inngest or Celery + Redis for durable workflows
Observability:  LangSmith or Langfuse + Braintrust (evals)
Deployment:     Kubernetes (stateful agents) or Restate (durable serverless)
```

### Common Production Pitfalls

**1. Infinite Loop / Runaway Loops** (most common failure mode)
- **Fix**: Hard step limits (max 25 iterations), watchdog process with timeout kill, LangGraph `recursion_limit`, circuit breakers on tool errors

**2. Hallucination Cascades** (multi-agent: a sub-agent hallucinates → downstream executes on it)
- **Fix**: Schema validation at every agent boundary, CHARM framework for cascade detection, never pass unvalidated outputs to write-path tools

**3. Cost Explosions** (single run consuming 10M+ tokens)
- **Fix**: Token budget constraints in system prompt, per-run cost monitoring in LangSmith, billing alerts at $10/run, tiered model routing

**4. Tool Call Amplification** (tools returning large payloads triggering more calls)
- **Fix**: Tool response size limits (truncate at 4K tokens), pagination with `page_size/page_token`, summary-first responses

**5. Shadow Agent Sprawl** (agents deployed without governance)
- **Fix**: Central agent registry, per-agent permission scopes, mandatory audit logging, Agent Owner policy

---

## 7. OBSERVABILITY STACK

| Platform | Strengths | Best For |
|----------|-----------|---------|
| LangSmith | Deepest LangGraph integration, node-by-node state diffs, replay | LangChain shops |
| Langfuse | Open-source, self-hostable, SOC2, EU compliance (acquired by ClickHouse Jan 2026) | Cost/compliance sensitive |
| Arize Phoenix | ML-grade eval, drift detection, embeddings analysis | Eval rigor + enterprise |
| Braintrust | Observability + eval in one platform, CI/CD eval gates | Quality-driven teams |
| Datadog LLM | Enterprise Datadog shops; only LLM spans billed | Existing Datadog |

**Mature pattern**: LangSmith/Langfuse (runtime tracing) + Braintrust (automated eval gates in CI/CD). Tracing tells you what happened; eval gates tell you if it was good.

---

## 8. DEPLOYMENT ARCHITECTURE

### Kubernetes vs. Serverless Decision

| Criteria | Kubernetes | Durable Serverless (Restate/Inngest) |
|----------|-----------|--------------------------------------|
| Long-running tasks (minutes+) | Excellent | Challenging without durable layer |
| Cost model | Fixed capacity | Pay-per-execution |
| Failure recovery | Manual retries | Automatic with Restate/Inngest |
| Cold starts | None | 200ms–3s (GPU) |
| Best for | High-concurrency, stateful agents | Event-driven, bursty, short tasks |

**2026 emerging pattern**: Durable serverless with Restate or Inngest — serverless economics + Kubernetes-grade reliability. Automatic retry, persistent state, saga orchestration, <200ms cold starts.

### Async Architecture

```
User Request → API Gateway → Task Queue (SQS/Redis)
                                    ↓
                         Worker Pool (agent runners)
                                    ↓
                         Result Store (S3/Redis)
                                    ↓
                         Webhook / SSE Push to Client
```

**Worker queue selection**:
- **Inngest**: TypeScript/Python, durable workflows, step retries, fan-out native. Better DX than Celery for complex agent graphs
- **Celery + Redis**: Mature, Python-native, excellent LangChain integration
- **AWS SQS + Lambda**: Best for bursty workloads in AWS shops
- **Modal/RunPod**: GPU-backed serverless for agents requiring local model inference

### Latency Optimization

1. Parallel tool execution (LangGraph supports parallel node execution)
2. Response streaming — stream tokens to user immediately; critical for >5s tasks
3. Pre-warming context: pre-compute and cache frequently-used retrieval at session start
4. Model warming: keep containers warm with periodic ping tasks

---

## 9. AGENTIC PRODUCT PATTERNS

### Autonomy Spectrum

```
Level 0: Suggestion Only     — AI recommends, human acts
Level 1: Draft + Approve     — AI drafts action, human approves before execution
Level 2: Execute + Notify    — AI executes, notifies after (default for low-risk)
Level 3: Execute + Audit     — AI executes autonomously, full audit trail, periodic review
Level 4: Full Autopilot      — AI executes + self-corrects, human reviews exceptions only
```

**Decision rule**: Autonomy level = f(reversibility × impact). Sending an email (irreversible, high impact) → Level 1. Updating an internal tag (reversible, low impact) → Level 3+.

### Human-in-the-Loop (HITL) with LangGraph

```python
# interrupt_before: pause before node executes (approval gate)
graph.compile(interrupt_before=["send_email", "execute_trade", "delete_record"])

# interrupt_after: pause after node executes (review gate)
graph.compile(interrupt_after=["draft_document", "generate_report"])
```

On interrupt: state checkpoints to PostgreSQL; frontend presents pending action; on approval call `graph.update_state(thread_id, approved_values)` and resume. Supports async HITL — agent can pause for hours waiting for human input.

### Audit Trail Requirements

Every production agent action must log:
- `agent_id`, `agent_version`, `run_id`, `timestamp` (ISO 8601 UTC)
- `action_type`, `input_hash`, `output_hash`
- `human_approver` (if applicable)
- `policy_applied`, `cost_incurred` (tokens + API costs)

Store in append-only immutable log.

---

## 10. AGENTIC SECURITY

### OWASP LLM Top 10 (2025) — Agentic-Critical Risks

**LLM01: Prompt Injection** (#1 risk)
In agentic systems with tool access, successful injection executes code, calls APIs, exfiltrates data. **Indirect injection** (from web content, email bodies, database records processed by agent) is the primary production attack vector.

**LLM06: Excessive Agency**
Agents granted permissions beyond what the task requires. Principle of least privilege is systematically violated in agentic systems.

**LLM07: System Prompt Leakage**
Real-world incidents show attackers extracting internal rules, permission structures from system prompts. Never store credentials in system prompts.

### MCP-Specific Security Threats

**Tool Poisoning**: Malicious instructions embedded in MCP tool metadata (descriptions, parameter descriptions). Tools can mutate their definitions after installation. **5 of 7 major MCP clients lacked static validation of server-provided metadata** (arxiv:2603.22489).

**Rug Pull Attack**: Trusted MCP server updates tool definitions post-security review to include malicious behavior.

**Cross-Agent Injection**: Compromised agent injects instructions into messages passed to another. Treat all inter-agent messages as untrusted input.

### Defense Architecture

```
Input Layer:    Content filtering on all user inputs + retrieved documents
Tool Layer:     Least-privilege tool scopes, read/write separation, no credentials in context
Execution Layer: Step limits, circuit breakers, watchdog processes
Output Layer:   Schema validation before downstream consumption
Audit Layer:    Immutable append-only logs with input/output hashes
Human Layer:    Interrupt gates on irreversible/high-impact actions
```

**Anti-exfiltration**: HashiCorp Vault or AWS Secrets Manager separate from agent code; credentials injected at runtime, never in context; block agent access to egress paths not explicitly whitelisted.

---

## 11. FRAMEWORK COMPARISON

### Selection Matrix (2026)

| Framework | Stars | Best For | Tradeoff |
|-----------|-------|---------|---------|
| **LangGraph** | #1 enterprise | Complex stateful agents, HITL, regulated industries | Highest learning curve, most boilerplate |
| **CrewAI** | 31.2K (+1,014% since 2024) | Rapid prototyping, role-based workflows | 3x token overhead on simple tasks |
| **AutoGen / AG2** | Growing | Complex conversations, Microsoft/Azure ecosystem | Merged with Semantic Kernel |
| **Agno** (ex-Phidata) | 39K | Performance-critical, high-concurrency, multimodal | 2μs agent instantiation, 3.75 KiB/agent (10,000x faster than LangGraph) |
| **OpenAI Agents SDK** | — | OpenAI-native agents, handoff routing | First-party OpenAI support |
| **Google ADK** | — | Vertex AI/Gemini ecosystem, A2A protocol | April 2025, GCP-only native support |

### Decision Guide

```
Q1: Single task or multi-step workflow?
  Single → Direct SDK call + minimal wrapper
  Multi-step → Q2

Q2: Does state persist across sessions or require HITL?
  No  → Simple loop + step counter + circuit breaker
  Yes → LangGraph + PostgresSaver checkpointer

Q3: Single agent or multiple?
  Single → LangGraph single-agent graph
  Multiple → Q4

Q4: Role-based or dynamic planning?
  Role-based, fast prototype → CrewAI
  Complex state + production → LangGraph supervisor
  Performance-critical → Agno
  Google ecosystem → Google ADK
```

**The Framework Tax**: LangGraph adds ~50-100ms overhead per compilation; CrewAI adds tokens every step. For simple single-agent workflows, a direct SDK call with a few hundred lines of Python often outperforms and outlasts any framework. Start with the simplest thing that works.

---

## 12. INVESTOR METRICS FOR AGENTIC AI

### Market Context

- **2025**: $6.42B raised by agentic AI companies globally (>25% of all capital ever deployed in the sector)
- **2026**: $2.66B across 44 rounds through April — 144% YoY increase but 39% fewer deals; average round $155M
- **Gartner warning**: >40% of agentic AI projects predicted to be canceled by end of 2027 due to cost, unclear business value, or weak risk controls

### Key Production Metrics VCs Ask For

**Task Completion Rate (TCR)**: % of tasks where agent achieves intended goal.
- Demo-grade: 70%
- Business-grade: >85%
- World-class: WebArena SOTA ~60%+ (human baseline 78%)

**Autonomy Index (AIx)**:
```
AIx = 1 - (human_interventions / total_steps)
```
40% intervention rate = "very needy chatbot." Target: AIx >0.85 to claim production autonomy.

**Cost per Successful Task (CpST)**: Not cost per token — cost per business outcome. Leading agents show 50x variation ($0.10–$5.00/task) for comparable accuracy. Use the CLEAR framework: Cost, Latency, Efficacy, Assurance, Reliability.

**ROI benchmarks (2026)**: ~6.4 hours saved per knowledge worker per week; 9–66x cost-per-task reductions; payback periods of 4–9 months.

### What VCs Fund in 2026

1. Exclusive industry data or proprietary knowledge graphs
2. Measurable, high-cost, high-frequency workflow with a clear enterprise buyer
3. Systematic eval framework showing >500 edge case performance
4. EU AI Act compliance plan, GDPR/HIPAA data handling
5. Production metrics (not demo performance)

**Instant disqualifications**: "ChatGPT wrapper" without data moat; undifferentiated "autonomy platform" without a specific workflow; no eval framework beyond internal demos.
