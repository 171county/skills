# MCP Future & Roadmap

## The 2026-07-28 Release Candidate: "MCP 2.0"

The most significant protocol revision since launch. Currently in RC as of mid-2026, targeting stable release in late 2026.

### Stateless Protocol Core (The Biggest Change)
**What:** The `initialize`/`initialized` handshake is removed. Protocol-level sessions (`Mcp-Session-Id`) are eliminated.

**How it works:** Client capabilities that previously traveled once at initialization now travel in `_meta` on every request. A new `server/discover` method lets clients fetch server capabilities on demand when needed.

**Impact on infrastructure:**
- Remote MCP servers that previously required sticky sessions, shared session stores, and deep packet inspection at the gateway can now run behind **plain round-robin load balancers**
- Serverless/ephemeral compute (Lambda, Cloud Run) becomes viable for MCP servers
- Horizontal scaling is trivially easy
- Cold starts are no longer a problem

**Migration guide:**
- If using stateful session data: move to explicit handle patterns (pass IDs as tool parameters)
- If using `roots` for filesystem scoping: pass as tool parameters or resource URI parameters
- If using `sampling`: migrate to direct LLM API integration
- If using MCP `logging`: migrate to stderr or OpenTelemetry

### Deprecations in 2026 RC
All three are deprecated (functional but not recommended for new code):

**Sampling deprecated** — replaced by direct LLM API integration.
The `sampling/createMessage` mechanism where servers request LLM completions from clients is deprecated. The reasoning: direct LLM API calls give servers more control and eliminate the human-in-the-loop approval latency that sampling requires. The replacement is for the server to directly call an LLM API (e.g., Anthropic's API) when it needs LLM reasoning.

Note: There is debate about this deprecation in the community (nullpointer.se), as sampling was useful for servers that don't have their own API keys. Evaluate whether you need sampling before building on it for new implementations.

**Roots deprecated** — replaced by tool parameters or resource URI encoding.
Pass filesystem paths, workspace directories, and access scopes as explicit tool parameters rather than using the roots mechanism.

**Logging deprecated** — replaced by stderr or OpenTelemetry.
The MCP logging capability (sending log messages from server to client) is removed in favor of standard infrastructure observability patterns.

### Extensions Framework (Formal Governance)
The November 2025 spec introduced experimental extensions. The 2026 RC formalizes them:

**Extension governance:**
- Reverse-DNS identifiers (e.g., `io.anthropic.computer-use`)
- Independent versioning per extension
- Dedicated extension repositories
- Conformance testing requirements

**Official Extensions Debuting in 2026 RC:**

**MCP Apps** — server-rendered HTML interfaces in sandboxed iframes. Enables MCP servers to provide lightweight UI components that render inside AI clients. Example: a search MCP server that renders a result grid, or a database MCP server that renders a table view.

**Tasks (as Extension)** — the async task execution system graduates from experimental core feature to a formal extension, redesigned around stateless operations. Task state is stored externally and referenced by handle, compatible with the stateless protocol core.

### Authorization Hardening (2026 RC)
Six proposals harden OAuth alignment:
- Issuer validation per RFC 9207
- Application type declarations during Dynamic Client Registration
- Strengthened audience binding requirements
- OpenID Connect standard alignment

### Full JSON Schema 2020-12 for Tools
Tools now use full JSON Schema 2020-12 for `inputSchema` and `outputSchema`, enabling more expressive type definitions, `$defs` for reusable schemas, and better validation tooling.

### Deprecation Policy Guarantee
The 2026 RC introduces formal policy (SEP-2596): minimum **12-month overlap** between deprecation announcement and removal. Features deprecated in 2026-07-28 RC cannot be removed before July 2027. This makes MCP a stable foundation for long-term production use.

---

## Governance: The Agentic AI Foundation (AAIF)

### December 2025: Anthropic Donates MCP to Linux Foundation

Anthropic co-founded the **Agentic AI Foundation (AAIF)** as a directed fund under the Linux Foundation, alongside OpenAI and Block. Inaugural projects:
- **MCP** (Anthropic) — the tool connectivity standard
- **goose** (Block) — open-source AI agent
- **AGENTS.md** (OpenAI) — agent behavior specification standard

**What this means strategically:**
- MCP is now **vendor-neutral infrastructure**, not an Anthropic product feature
- Governance is community-driven, not unilateral
- Industry can build on MCP with confidence it won't be controlled by one vendor
- Major cloud providers (AWS, Google Cloud, Azure) and AI companies (OpenAI, Microsoft) are actively contributing

**Signal to enterprise buyers:** This is the equivalent of HTTP being standardized — a foundational technology the entire industry will build on for decades.

---

## Industry Trajectory

### The Multi-Client Convergence
MCP is now the **universal AI-to-tool interface**:
- **Claude** (Desktop, Code, Web, API)
- **ChatGPT** (Desktop app, ChatGPT apps — adopted March 2025, deployed September 2025)
- **Cursor** — primary AI coding environment
- **GitHub Copilot** — VS Code, GitHub.com
- **Microsoft Copilot** — M365 integration
- **Gemini** — Google Workspace integration
- **VS Code** — native MCP client

With 97M monthly SDK downloads and 10,000+ active servers, MCP has crossed the network effect threshold — it's cheaper to support MCP than to build point-to-point integrations.

### The Three Waves of MCP Adoption

**Wave 1 (2024-early 2025): Developer Tooling**
Early adopters were developer tools: Claude Desktop users, Cursor users, VS Code Copilot. Use cases: file system access, git operations, code search.

**Wave 2 (mid-2025): Enterprise SaaS Integration**
Major SaaS vendors (Notion, Stripe, GitHub, Atlassian) launched official MCP servers. Enterprise teams began using MCP as their primary AI integration layer for internal tools.

**Wave 3 (2026+): AI-Native Products & Agents**
MCP-first products designed for AI consumption. Multi-agent architectures where MCP is the inter-agent communication layer. Enterprise AI platforms with governed MCP registries. Serverless MCP (enabled by 2026 stateless spec).

### Emerging Patterns to Watch

**1. MCP as an Agent Orchestration Bus**
Multi-agent systems using MCP not just for tool access but for agent-to-agent communication. Specialist agents wrapped as MCP servers, orchestrators calling them like tools.

**2. Serverless MCP (Post-2026 RC)**
The stateless spec makes ephemeral serverless MCP servers viable. Function-as-a-service MCP tools with sub-100ms cold starts and automatic scaling.

**3. MCP Marketplaces with Per-Call Pricing**
Emerging business model: pay-per-tool-call MCP services. Analytics, enrichment, search, specialized AI capabilities sold as MCP APIs. Users install from marketplace and pay based on usage.

**4. Personal MCP Registries**
Individuals maintaining their own MCP server collections configured precisely for their workflow — a "personal tool stack" that travels with them across any MCP-compatible AI client.

**5. MCP in Edge/Mobile**
MCP servers deployed at edge locations close to data. Mobile apps exposing local data (contacts, calendar, photos) via MCP to on-device AI models.

**6. Computer Use + MCP Fusion**
Combining Anthropic's computer use capability with MCP tools: AI agents that can use both structured tool APIs (via MCP) and unstructured UI automation for services without APIs.

---

## What MCP Does NOT Address (Honest Limitations)

**1. Discoverability at scale**: With 10,000+ servers, finding the right MCP server for a task is unsolved. Search in registries is primitive. Expect significant improvements in 2026.

**2. Tool quality variance**: Anyone can publish an MCP server. Quality varies enormously. The ecosystem needs better quality signals (ratings, usage counts, security audits).

**3. Cross-server state**: MCP has no built-in mechanism for data flowing between servers. If tool A returns data needed by tool B on a different server, the model must pass it explicitly. The explicit handle pattern helps but adds verbosity.

**4. Real-time streaming tool results**: Long-running tools with partial streaming results are an active area of work. Tasks (November 2025) help but don't provide streaming partial results.

**5. Tool versioning and migration**: When a server upgrades and changes a tool's schema, there's no built-in mechanism to negotiate which version a client uses. Breaking changes require careful coordination.

**6. Sampling deprecation ambiguity**: The upcoming deprecation of `sampling` removes a useful capability without a clean replacement for servers that want to invoke LLM reasoning without managing their own API keys. This may be revisited before the final 2026 spec.
