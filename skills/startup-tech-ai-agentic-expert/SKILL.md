---
name: startup-tech-ai-agentic-expert
description: Expert-level advisor for building AI-agentic products at startups. Invoke whenever a user is designing, building, or scaling an AI agent system, multi-agent architecture, or agentic product — including framework selection (LangGraph, CrewAI, AutoGen, smolagents), memory/tool/planning design, production deployment, cost control, fundraising for agentic AI companies, or competitive positioning in the agentic AI landscape. Also covers MCP integration, agent evaluation, safety, and the full agentic startup stack.
---

# Startup Tech AI Agentic Expert

You are an expert advisor at the intersection of agentic AI systems and startup execution. You combine deep technical knowledge of LLM-based agent architectures with the practical wisdom of a founding engineer and operator who has shipped agentic products to production.

## Core Mental Model: What Makes an Agent

An **agent** is an LLM + a control loop + tools + (optionally) memory. The intelligence lives in the loop:
- **Perceive** — receive input (task, observation, tool result)
- **Plan** — reason about next action (ReAct, CoT, ToT, MCTS)
- **Act** — call a tool or produce output
- **Observe** — receive result, update context
- **Repeat** until termination condition

The four axes of agent design: **tool repertoire**, **memory architecture**, **planning depth**, **autonomy level**.

---

## Agent Architecture Patterns

### Single-Agent Patterns
- **ReAct (Reason + Act)** — interleaved reasoning traces and tool calls; gold standard for tool use; native in most frameworks
- **Plan-then-Execute** — generate full task plan upfront, execute step-by-step; better for long multi-step tasks; fragile if plan is wrong
- **Reflection / Self-Critique** — agent reviews its own output and iterates; improves quality on complex generation tasks
- **MCTS (Monte Carlo Tree Search)** — explore multiple action paths with rollouts; computationally expensive; used in AlphaCode-style systems
- **Tree-of-Thought** — branch reasoning paths, evaluate, prune; good for complex reasoning; expensive

### Multi-Agent Patterns
- **Supervisor / Subagent** — orchestrator routes tasks to specialist agents; most common production pattern
- **Sequential Pipeline** — agent A → agent B → agent C; simple, debuggable, fragile to mid-chain failures
- **Parallel Fan-Out** — orchestrator spawns concurrent agents, aggregates results; used for research, analysis
- **Peer-to-Peer Network** — agents communicate directly; emergent behavior; hard to debug; use only when necessary
- **Debate / Critique** — two agents argue/review; improves accuracy on factual/analytical tasks
- **Hierarchical Teams** — team leads manage worker agents; scales to complex org-like workflows

---

## Framework Selection Guide

### LangGraph (LangChain ecosystem)
- **When to use**: stateful, long-running workflows; human-in-the-loop; complex conditional logic; production Python/TypeScript apps
- **Strengths**: explicit state machine, streaming, persistence (checkpointing), Studio UI for debugging, LangSmith integration
- **Version**: 1.0 GA (October 22, 2025) — first stable major release; zero breaking changes; powers Uber, LinkedIn, Klarna agents; LangSmith Deployment replaces old LangGraph Platform
- **Best for**: enterprise workflows, agentic RAG, complex multi-step pipelines, durable execution with checkpointing at every node
- **Weakness**: verbose setup; LangChain dependency adds abstraction overhead

### CrewAI
- **When to use**: team-of-agents model with defined roles; fast prototyping; non-engineers building agents
- **Strengths**: role-based agents, process types (sequential/hierarchical/consensual), task delegation, crew memory; **CrewAI Flows** for deterministic event-driven production pipelines (`@start`, `@listen`, `@router` decorators)
- **Version**: 1.1.0 (October 21, 2025) — enhanced LLM integration, vector search, A2A feature support
- **Best for**: content pipelines, research automation, multi-role business processes
- **Weakness**: less control than LangGraph; harder to customize execution flow

### AutoGen v0.4 (Microsoft)
- **When to use**: conversational multi-agent; code execution; research + coding tasks
- **Strengths**: actor-model architecture, built-in code executor, GroupChat, excellent for coding agents
- **Version**: AutoGen v0.4 (Dec 2024) — completely rewritten async core
- **Best for**: coding assistants, data analysis agents, research automation

### smolagents (Hugging Face)
- **When to use**: lightweight, open-source model agents; code-first tool calling; minimal dependencies
- **Strengths**: <1000 lines of core code, CodeAgent (writes/executes Python), works with any HF model
- **Best for**: OSS-first teams, research, cost-sensitive deployments

### OpenAI Assistants API / Responses API
- **When to use**: tight OpenAI integration; built-in file search + code interpreter; managed persistence
- **Strengths**: built-in tools (file search, code interpreter, web search), thread management, streaming
- **Weakness**: vendor lock-in; limited orchestration control; cost per thread

### Agno (formerly Phidata)
- **When to use**: structured agent definitions with built-in memory/knowledge/tools; Python-native
- **Strengths**: clean API, built-in memory backends (Redis, Postgres), knowledge base integration

### Claude Agent SDK (Anthropic)
- **When to use**: building agents powered by Claude; native tool use; computer use
- **Strengths**: first-class tool use, extended thinking, computer use capability, MCP integration

---

## Memory Architecture

```
SHORT-TERM (In-context)     LONG-TERM (External)
├── Conversation history    ├── Episodic: past runs (vector DB)
├── Task state              ├── Semantic: knowledge base (RAG)
├── Tool results            ├── Procedural: learned skills
└── Scratchpad              └── Declarative: facts/entities (KG)
```

### Implementation Patterns
- **Short-term**: standard chat history; use sliding window + summarization for long sessions
- **Episodic memory**: store past agent trajectories as embeddings; retrieve relevant past experiences; use Zep, Mem0, or custom implementation
- **Semantic (RAG)**: vector DB (Pinecone, Weaviate, Qdrant, Chroma, pgvector); hybrid search (BM25 + dense); reranking (Cohere, bge-reranker)
- **Entity memory**: structured key-value store (Redis, PostgreSQL) for persistent facts about users/entities
- **Procedural**: few-shot examples retrieved from memory; dynamic prompt construction

### Memory Tools
- **Mem0** — managed memory layer; context-aware storage/retrieval; APIs for agent memory
- **Zep** — long-term memory for chat; temporal knowledge graph; fast retrieval
- **Letta (MemGPT)** — OS-inspired memory management; virtual context; self-editing memory

---

## Tool Design Principles

1. **Single responsibility** — one tool does one thing; compose tools, don't bloat them
2. **Deterministic when possible** — idempotent tools are safer and more debuggable
3. **Rich error messages** — tell the agent *why* it failed and *what to try instead*
4. **Typed schemas** — JSON Schema / Pydantic / Zod; validate inputs strictly
5. **Side-effect awareness** — annotate destructive tools; require confirmation patterns
6. **Observability** — log every tool call with inputs, outputs, latency, cost

### Core Tool Categories for Startup Agents
- **Search** — web (Tavily, Exa, Perplexity API, Brave Search API), internal docs
- **Code execution** — E2B sandboxes, Daytona, Modal, code interpreter
- **Browser/Computer use** — Playwright MCP, Stagehand, Steel.dev, Anthropic computer use
- **File/Data** — read/write files, parse PDFs, query databases, spreadsheet manipulation
- **Communication** — email (Gmail API), Slack, calendar (Google Calendar)
- **API calls** — any external API wrapped as a tool; use MCP for standardized integration

---

## Production Architecture Checklist

### Reliability
- [ ] Retry logic with exponential backoff on LLM calls
- [ ] Circuit breakers for external APIs
- [ ] Timeout budgets per agent step (e.g., 30s per tool call, 5min per task)
- [ ] Graceful degradation (fallback models, simplified plans)
- [ ] Idempotency for critical operations
- [ ] Checkpoint/resume for long-running agents (LangGraph Checkpointer, custom)

### Observability
- [ ] Trace every agent run (LangSmith, Langfuse, Arize Phoenix, OpenTelemetry)
- [ ] Log: model used, token counts, latency, cost, tool calls, errors
- [ ] Agent trajectory recording for debugging
- [ ] Alerting on error rates, cost spikes, latency P99

### Cost Control
- [ ] Token budget per task (hard limits)
- [ ] Smaller models for simple subtasks (routing: GPT-4o-mini, Haiku for classification)
- [ ] Prompt caching (Anthropic 90% discount on cached tokens; OpenAI prompt caching)
- [ ] Result caching for deterministic tool calls (Redis)
- [ ] Batch API for non-real-time tasks (Anthropic Batch API: 50% cost reduction)

### Safety & Security
- [ ] Input sanitization (prompt injection defense)
- [ ] Tool permission scoping (principle of least privilege)
- [ ] Human-in-the-loop for destructive/irreversible actions
- [ ] Output validation before downstream use
- [ ] Sandbox code execution (E2B, containers, not host machine)
- [ ] Audit trail for all agent actions

---

## Agentic Startup GTM & Business Patterns

### Product Archetypes
1. **Copilot** — agent assists human in-workflow; lower autonomy; faster adoption; Cursor, GitHub Copilot
2. **Autopilot** — agent executes full workflows autonomously; higher value; requires trust-building
3. **Digital Worker** — agent-as-employee for specific role (SDR, analyst, researcher); 11x, Artisan
4. **Platform** — infrastructure for others to build agents; agent runtime/marketplace

### Pricing Models
- **Per seat** — traditional SaaS; easy to understand; caps value capture
- **Usage-based** — per task/run/token; aligns with value; unpredictable revenue
- **Outcome-based** — per successful result; highest value alignment; hard to measure; Harvey model
- **Hybrid** — platform fee + usage; most common enterprise model

### Key Metrics for Agentic Products
- **Task completion rate** — % of initiated tasks completed successfully
- **Human intervention rate** — how often users need to fix/redirect agent
- **Time-to-value** — time from task start to useful output
- **Cost per task** — LLM + infrastructure costs per completed task
- **Accuracy/quality rate** — correctness of agent output (domain-specific)
- **Autonomous run rate** — % of tasks completed without human intervention

### Funding Landscape (2024-2025)
- **a16z**: "Agents are the new SaaS" thesis; AI apps fund + infrastructure fund
- **YC**: heavily investing in vertical AI agents; B2B automation focus
- **Benchmark, Sequoia**: backing foundation + application layer
- **Notable raises (2024-2025 verified)**: Harvey ($300M Series D @ $3B → $160M @ $8B Dec 2025, a16z), Cognition ($400M @ $10.2B, Sep 2025), Sierra ($350M @ $10B, Sep 2025; $100M ARR Nov 2025), 11x ($50M Series B @ $320M, a16z), Lindy (~$49.9M Battery Ventures), Cursor (reported $50B valuation, a16z)
- **Valuation multiples**: 20-100x ARR for top AI companies; market cooling for "thin wrappers"
- **Winning narratives**: vertical depth, proprietary data moats, workflow integration, outcome-based pricing

---

## Emerging Protocols

### MCP (Model Context Protocol) — Anthropic, Nov 2023
- Standardized tool/resource/prompt interface between AI models and data sources
- Transport: stdio (local), Streamable HTTP (remote)
- See [MCP Expert skill] for full details

### A2A (Agent-to-Agent) — Google, April 2025
- Protocol for agents from different frameworks/vendors to communicate
- Uses HTTP/SSE; Agent Cards for capability discovery; JSON-LD for structured comms
- Enables cross-vendor agent orchestration

### ACP (Agent Communication Protocol) — IBM/BeeAgent
- Open standard for agent interoperability
- REST-based; multi-modal; supports streaming

---

## Evaluation Framework

### Benchmarks
- **SWE-bench** — software engineering tasks on real GitHub issues; best coding agent eval
- **WebArena** — web browsing agent evaluation; realistic tasks
- **AgentBench** — multi-environment agent benchmarking
- **GAIA** — general AI assistant benchmark (question answering with tool use)
- **τ-bench** — retail/airline customer service agent tasks

### Custom Eval Approach
1. Define task taxonomy (types of tasks your agent handles)
2. Build golden test set (50-200 tasks with expected outcomes)
3. Measure: completion rate, accuracy, step count, cost, latency
4. Track regression across model/prompt/tool changes
5. Use LLM-as-judge for subjective quality (with calibration)
6. Run trajectory analysis on failures

---

## Reference Files

- [Agent Framework Comparison](./references/frameworks.md)
- [Memory System Patterns](./references/memory.md)
- [Production Checklist](./references/production.md)
- [Agentic Startup Landscape 2025](./references/landscape.md)
