# MCP Ecosystem Guide (2025-2026)

## Adoption Metrics (as of late 2025)
- **97 million monthly SDK downloads** across Python and TypeScript
- **10,000+ active MCP servers** in the wild
- **~2,000 entries** in the official MCP Registry (407% growth since September 2025)
- **58 spec maintainers**, 2,900+ Discord contributors, 100+ weekly new contributors
- **10 official SDK languages**: TypeScript, Python, Go, Rust, Java, C#, PHP, Ruby, Swift, Kotlin
- Industry standard status: adopted by Claude, ChatGPT, Cursor, Gemini, Microsoft Copilot, VS Code

---

## Official Reference Servers (Anthropic)
Maintained at `github.com/modelcontextprotocol/servers`. These are **reference implementations** demonstrating MCP features — not production-ready solutions. Evaluate your own security requirements.

| Server | Capabilities | Notes |
|--------|-------------|-------|
| **everything** | Prompts, resources, tools | Reference/test server demonstrating all MCP features |
| **fetch** | Tools | Web content retrieval optimized for LLM processing (HTML→markdown) |
| **filesystem** | Tools | Controlled file operations with configurable access restrictions |
| **git** | Tools | Repository reading, searching, manipulation |
| **memory** | Resources, Tools | Knowledge graph-based persistent memory across sessions |
| **sequential-thinking** | Tools | Problem-solving through dynamic thought sequences |
| **time** | Tools | Time and timezone conversion |

---

## First-Party Official MCP Servers (Major Vendors)
These are production-quality servers maintained by the service providers themselves.

### Development & Engineering
- **GitHub MCP** — Repository management, issues, PRs, Actions, code search. Most widely used MCP server.
- **GitLab MCP** — Full GitLab API coverage
- **Hugging Face MCP** — Model management, dataset search, inference API
- **Postman MCP** — API testing workflow automation

### Productivity & Communication
- **Notion MCP** — Notes, databases, pages management
- **Atlassian (Jira/Confluence) MCP** — Issue tracking, wiki
- **Slack MCP** — Message sending, channel management

### Infrastructure & Data
- **Supabase MCP** — Database operations, auth, edge functions (notable: involved in a CVE-2025 prompt injection incident)
- **Stripe MCP** — Payment workflows, customers, charges, subscriptions
- **Vercel MCP** — Deployment management, project configuration

### Cloud Platforms
- **AWS MCP** (awslabs/mcp) — Comprehensive AWS service coverage with enterprise-grade design guidelines. Uses Python with Pydantic, strict IAM documentation, security-first design.
- **Azure MCP** — Azure resource management
- **Google Cloud MCP** — GCP services

---

## MCP Registry & Discovery

### Official Registry
The official MCP Registry (`registry.modelcontextprotocol.io`) is the canonical discovery mechanism. It uses OAuth Client ID Metadata Documents for server identity. As of November 2025: ~2,000 entries.

### GitHub MCP Registry
GitHub launched its own MCP Registry as a discovery hub for developers using GitHub Copilot and other GitHub-integrated AI tools. Supports `gh` CLI-based installation.

### Community Curated Lists
- `awesome-mcp-servers` — Community curated GitHub repos
- `mcp.run` — Registry focused on hosted/remote servers
- `smithery.ai` — MCP server marketplace with managed hosting
- `mcp-get` — npm-style CLI for installing MCP servers

### Desktop Extensions (Claude Desktop)
Anthropic introduced Desktop Extensions in 2025 — a packaging format enabling **one-click installation** of local MCP servers in Claude Desktop. Instead of editing JSON configuration files and managing dependencies manually, users install `.dxt` packages like browser extensions. This dramatically lowered the barrier to adoption for non-developers.

---

## MCP in Different Claude Contexts

### Claude Desktop
- Configured via `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or equivalent
- Supports stdio-based local servers and remote Streamable HTTP servers
- Desktop Extensions (`.dxt` packages) for one-click installation
- Tools appear in the UI as available capabilities
- User sees tool call requests with approve/deny option

**Example config:**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "ghp_..." }
    },
    "remote-server": {
      "url": "https://my-mcp-server.com/mcp",
      "headers": { "Authorization": "Bearer ..." }
    }
  }
}
```

### Claude Code (CLI)
- MCP servers configured as plugins in `.claude/settings.json` or global `~/.claude/settings.json`
- Full support for both stdio and Streamable HTTP
- Permissions system for controlling which tools can be called without confirmation
- MCP servers appear as additional tools alongside Claude Code's built-in tools
- Community registry of Claude Code MCP plugins at the Claude Marketplace

**Example config:**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "${GITHUB_TOKEN}" }
    }
  }
}
```

### Claude.ai (Web)
- MCP integration in Claude.ai is more restricted than Desktop/Code
- Primarily through operator-configured integrations, not user-installable
- Remote MCP servers can be connected via UI settings for Pro/Team/Enterprise plans
- No access to local filesystem MCP servers (browser security)

### API (Direct)
- Claude API itself does not speak MCP directly — MCP is a client-side protocol
- To use MCP with the API, you run an MCP client library (e.g., `@modelcontextprotocol/client`) that manages server connections and converts MCP tool results into Claude API `tool_result` blocks
- The Anthropic Python/TypeScript SDKs have built-in MCP client support
- For programmatic use, the pattern is: MCP Client Library → MCP Server → Claude API

---

## Key Third-Party MCP Servers

### Web & Search
- **Brave Search MCP** — Real-time web search via Brave Search API (single `npx` install)
- **Perplexity MCP** — AI-powered web search and Q&A
- **Fetch MCP** (official) — General web content fetching and conversion

### Browser Automation
- **Puppeteer MCP** — Chrome automation, screenshots, form filling, web scraping
- **Playwright MCP** — Multi-browser automation with accessibility tree support

### Databases
- **SQLite MCP** — Local SQLite database operations
- **PostgreSQL MCP** — Production Postgres with schema introspection
- **Redis MCP** — Key-value store operations

### Observability & DevOps
- **Sentry MCP** — Error tracking and issue management
- **Datadog MCP** — Metrics, logs, APM
- **Kubernetes MCP** — Cluster management, pod operations

### AI/ML
- **Hugging Face MCP** — Model hub, datasets, inference
- **OpenAI MCP** — Official OpenAI tools server (after March 2025 adoption of MCP standard)

---

## MCP Across the Industry

### Multi-Platform Adoption (2025)
- **March 2025**: OpenAI officially adopted MCP across its products including ChatGPT desktop app
- **September 2025**: OpenAI added MCP support in ChatGPT apps for third-party access
- **December 2025**: Anthropic donated MCP to the Agentic AI Foundation (AAIF) under Linux Foundation — co-founded by Anthropic, OpenAI, Block
- **2026**: MCP is the de facto standard for AI-to-tool connectivity, running in Claude, ChatGPT, Cursor, Gemini, Microsoft Copilot, VS Code

### Gartner Signal
Gartner reports a **1,445% surge** in multi-agent system inquiries from Q1 2024 to Q2 2025, with MCP cited as the primary integration mechanism.

### MCP as Infrastructure
In December 2025, by donating to the Linux Foundation, Anthropic signaled that MCP is **infrastructure** — like HTTP, not like a product feature. The industry builds *on* it, not *around* it. This was the definitive signal that MCP won the standard war.
