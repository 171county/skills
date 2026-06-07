---
name: mcp-expert
description: Activate this skill when the user asks about MCP (Model Context Protocol), building MCP servers, implementing MCP clients, MCP tools/resources/prompts, MCP SDK usage (Python FastMCP, TypeScript SDK, Go, Rust), MCP transports (stdio, Streamable HTTP, SSE), MCP security and authentication (OAuth 2.1, PKCE), MCP deployment patterns (Docker, Cloudflare Workers, AWS Lambda), MCP ecosystem tools and registries, multi-agent patterns using MCP, MCP vs. OpenAI function calling vs. LangChain tools, the AAIF (AI Alliance Infrastructure Foundation) governance, MCP primitives (Tools, Resources, Prompts, Sampling, Roots, Elicitation), tool design best practices, MCP server marketplace and monetization, integrating MCP into Claude, ChatGPT, and other AI hosts, and connecting AI assistants to external systems, databases, APIs, and services through MCP. Use for any question about building with, deploying, or understanding the Model Context Protocol.
---

# MCP Expert

You are a world-class expert on the Model Context Protocol (MCP) — Anthropic's open standard for connecting AI assistants to external context and tools. You understand the protocol deeply: its architecture, all six primitives, transport mechanisms, security model, ecosystem, and production deployment patterns. You help developers build MCP servers, integrate MCP into applications, and leverage MCP to build powerful AI-powered workflows.

---

## 1. MCP Overview & Origin

### What Is MCP?

Model Context Protocol (MCP) is an open protocol announced by Anthropic on **November 25, 2024**. It solves the **N×M integration problem**: without MCP, connecting N AI applications to M data sources requires N×M custom integrations. With MCP, each tool implements one server standard, and each AI host implements one client standard → N+M integrations total.

**The USB-C analogy**: Just as USB-C standardized device connections so any device can connect to any peripheral, MCP standardizes how AI models connect to tools and data sources.

**Current scale** (mid-2025): 9,400+ MCP servers publicly available. Supported by Claude, ChatGPT, Gemini, Cursor, Windsurf, Continue, Sourcegraph Cody, and dozens of other AI applications.

**AAIF Governance**: On December 9, 2025, Anthropic donated MCP to the **AI Alliance Infrastructure Foundation (AAIF)** under the Linux Foundation. OpenAI, Google, Microsoft, and Amazon joined as platinum members — making MCP the industry-standard protocol.

### Architecture: Host / Client / Server

```
┌──────────────────────────────────────────────────────┐
│                     HOST                             │
│   (Claude Desktop, Cursor, your application)         │
│                                                      │
│  ┌─────────────────────────────────────────────────┐│
│  │                  MCP Client                     ││
│  │  (manages connections; sends JSON-RPC requests) ││
│  └───────────────────────┬─────────────────────────┘│
└──────────────────────────┼──────────────────────────┘
                           │  JSON-RPC 2.0
              ┌────────────┴──────────────┐
              │                           │
   ┌──────────▼──────────┐   ┌────────────▼──────────┐
   │    MCP Server A     │   │    MCP Server B        │
   │  (filesystem, DB,   │   │  (GitHub, Slack,       │
   │   internal APIs)    │   │   web search)          │
   └─────────────────────┘   └───────────────────────┘
```

**Host**: The application (Claude Desktop, Cursor, your custom app) that runs the MCP client and orchestrates the AI model.

**Client**: Protocol implementation that maintains 1:1 connections to MCP servers. Sends requests, receives responses.

**Server**: Exposes capabilities (Tools, Resources, Prompts) to clients. Servers are lightweight, focused programs.

---

## 2. Transport Mechanisms

### stdio (Standard Input/Output)

**Use for**: Local processes (Claude Desktop plugins, CLI tools, local development).

```
Host process spawns server as subprocess
Communication via stdin/stdout
Messages are newline-delimited JSON
No network overhead; highly secure (no open ports)
```

### Streamable HTTP (Standard for Remote Servers)

**Replaced SSE (Server-Sent Events) as the standard** in MCP spec version March 2025.

**Single endpoint**: `POST /mcp` (replaces separate `/sse` and `/messages` endpoints)

```
Client → POST /mcp (with JSON-RPC request body)
Server → HTTP response:
  - application/json (for simple request/response)
  - text/event-stream (for streaming responses with multiple events)
```

**Advantages**:
- Works with standard HTTP infrastructure (load balancers, CDN, API gateways)
- Single endpoint simplifies deployment
- Stateless design enables horizontal scaling
- Compatible with serverless functions

### Transport Selection Guide

| Transport | Use Case | Authentication | Scalability |
|---|---|---|---|
| stdio | Local tools, Claude Desktop | OS-level process isolation | Single user |
| Streamable HTTP | Remote servers, SaaS integrations | OAuth 2.1, API keys | Horizontally scalable |
| (Legacy SSE) | Older clients | Headers | Limited |

---

## 3. The Six MCP Primitives

### Primitive 1: Tools (Model-controlled)

Tools are functions the AI model can invoke. The model decides when and how to call them.

**Tool Definition Structure**:
```json
{
  "name": "search_database",
  "description": "Search the product database for items matching a query. Returns up to 20 results with name, price, and availability.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "Search query text"
      },
      "category": {
        "type": "string",
        "enum": ["electronics", "clothing", "home"],
        "description": "Optional category filter"
      },
      "max_results": {
        "type": "integer",
        "minimum": 1,
        "maximum": 20,
        "default": 10
      }
    },
    "required": ["query"]
  },
  "annotations": {
    "readOnly": true,
    "safe": true,
    "idempotent": true,
    "openWorld": false
  }
}
```

**Tool Annotations** (MCP spec 1.1+):
- `readOnly`: Tool does not modify state
- `safe`: Tool is safe to call without user confirmation
- `idempotent`: Multiple identical calls produce same result
- `openWorld`: Tool may have side effects beyond the AI's context

**Return Format**:
```json
{
  "content": [
    {"type": "text", "text": "Search results: ..."},
    {"type": "image", "data": "base64...", "mimeType": "image/png"},
    {"type": "resource", "resource": {"uri": "file:///path/to/file"}}
  ],
  "isError": false
}
```

### Primitive 2: Resources (Application-controlled)

Resources expose data that the host application can read and include in context. The **application** (not the model) decides what resources to include.

```
Resource URI Patterns:
  file:///path/to/document.txt   → Local files
  database://table/record-id     → Database records
  github://org/repo/path/file    → Remote resources
  custom://namespace/identifier  → Custom schemes
```

**Resource with URI Template**:
```json
{
  "uri": "database://customers/{customerId}/orders",
  "name": "Customer Orders",
  "description": "All orders for a specific customer",
  "mimeType": "application/json"
}
```

**Resource Subscriptions**: Clients can subscribe to resources and receive `notifications/resources/updated` when content changes. Enables live-updating context.

### Primitive 3: Prompts (User-controlled)

Prompts are reusable prompt templates that users can invoke. Appear as slash commands or template libraries in the host UI.

```json
{
  "name": "summarize-code-review",
  "description": "Summarize the key points from a code review",
  "arguments": [
    {
      "name": "pr_url",
      "description": "URL of the pull request",
      "required": true
    },
    {
      "name": "focus_area",
      "description": "Specific area to focus on (security, performance, etc.)",
      "required": false
    }
  ]
}
```

### Primitive 4: Sampling (Server-initiated LLM calls)

**The most powerful primitive**: Servers can request the host to perform LLM completions. Enables:
- Recursive agents (server calls LLM, which calls server tools, which calls LLM again)
- Server-side reasoning without direct model access
- Agentic behaviors initiated from the tool layer

```json
// Server sends sampling request to host
{
  "method": "sampling/createMessage",
  "params": {
    "messages": [{"role": "user", "content": "Analyze this data: ..."}],
    "modelPreferences": {
      "hints": [{"name": "claude-sonnet-4-6"}],
      "costPriority": 0.3,
      "speedPriority": 0.7
    },
    "maxTokens": 1024
  }
}
```

### Primitive 5: Roots

Servers can declare **roots** — URI boundaries where they have read/write access. Helps hosts understand server scope.

```json
{
  "uri": "file:///home/user/project",
  "name": "Project Root"
}
```

### Primitive 6: Elicitation

Servers can request additional information from users via structured forms. Enables multi-step workflows where the server needs user input mid-execution.

```json
{
  "method": "elicitation/create",
  "params": {
    "message": "Please provide your database credentials",
    "requestedSchema": {
      "type": "object",
      "properties": {
        "host": {"type": "string"},
        "password": {"type": "string", "format": "password"}
      }
    }
  }
}
```

---

## 4. Building MCP Servers

### Python FastMCP (Recommended)

```python
from fastmcp import FastMCP
from fastmcp.resources import FileResource
import httpx

mcp = FastMCP("My Data Server", version="1.0.0")

# Tool with type hints (FastMCP infers schema automatically)
@mcp.tool(
    description="Fetch current weather for a city",
    annotations={"readOnly": True, "safe": True}
)
async def get_weather(
    city: str,
    units: str = "celsius"
) -> str:
    """Returns current weather conditions."""
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"https://api.weather.example.com/current",
            params={"city": city, "units": units}
        )
        data = response.json()
        return f"Temperature in {city}: {data['temp']}°{units[0].upper()}, {data['description']}"

# Resource
@mcp.resource("database://products/{product_id}")
async def get_product(product_id: str) -> str:
    """Retrieve product details from database."""
    product = await db.products.get(product_id)
    return product.to_json()

# Prompt template
@mcp.prompt()
def analyze_sales_data(period: str = "last_quarter") -> list[dict]:
    return [
        {
            "role": "user",
            "content": f"Analyze our sales performance for {period}. Focus on: top products, regional breakdown, and growth trends."
        }
    ]

# Run as stdio (Claude Desktop) or HTTP server
if __name__ == "__main__":
    import sys
    if "--http" in sys.argv:
        mcp.run(transport="streamable-http", host="0.0.0.0", port=8080)
    else:
        mcp.run(transport="stdio")
```

### TypeScript SDK

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "database-server",
  version: "1.0.0",
});

// Tool with Zod schema validation
server.tool(
  "query_database",
  "Execute a read-only SQL query against the analytics database",
  {
    sql: z.string().describe("SELECT query to execute"),
    limit: z.number().min(1).max(100).default(10).describe("Max rows to return"),
  },
  async ({ sql, limit }) => {
    // Validate: only allow SELECT statements
    if (!sql.trim().toUpperCase().startsWith("SELECT")) {
      return {
        content: [{ type: "text", text: "Error: Only SELECT queries are allowed" }],
        isError: true,
      };
    }
    
    const results = await db.query(`${sql} LIMIT ${limit}`);
    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(results, null, 2),
        },
      ],
    };
  }
);

// Resource
server.resource(
  "schema://tables",
  "database schema listing all available tables and columns",
  async () => ({
    contents: [
      {
        uri: "schema://tables",
        text: await db.getSchemaDescription(),
        mimeType: "text/plain",
      },
    ],
  })
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Go SDK

```go
package main

import (
    "context"
    "encoding/json"
    "github.com/modelcontextprotocol/go-sdk/mcp"
)

func main() {
    server := mcp.NewServer("analytics-server", "1.0.0")
    
    server.AddTool(mcp.Tool{
        Name:        "get_metrics",
        Description: "Retrieve business metrics for a date range",
        InputSchema: json.RawMessage(`{
            "type": "object",
            "properties": {
                "metric": {"type": "string", "enum": ["revenue", "users", "conversions"]},
                "start_date": {"type": "string", "format": "date"},
                "end_date": {"type": "string", "format": "date"}
            },
            "required": ["metric", "start_date", "end_date"]
        }`),
        Handler: func(ctx context.Context, params map[string]interface{}) (*mcp.ToolResult, error) {
            // Fetch and return metrics
            return &mcp.ToolResult{
                Content: []mcp.Content{{Type: "text", Text: "Metrics data..."}},
            }, nil
        },
    })
    
    server.ServeStdio()
}
```

### Rust rmcp

```rust
use rmcp::{ServerHandler, tool, model::*, service::RequestContext};

#[derive(Clone)]
struct FileServer;

#[rmcp::server_handler]
impl ServerHandler for FileServer {
    fn get_info(&self) -> ServerInfo {
        ServerInfo {
            name: "file-server".into(),
            version: "1.0.0".into(),
            ..Default::default()
        }
    }
    
    #[tool(description = "Read a file's contents from the filesystem")]
    async fn read_file(
        &self,
        #[tool(param)] path: String,
        _ctx: RequestContext<RoleServer>,
    ) -> Result<String, rmcp::Error> {
        tokio::fs::read_to_string(&path)
            .await
            .map_err(|e| rmcp::Error::internal(e.to_string()))
    }
}

#[tokio::main]
async fn main() {
    let server = FileServer;
    let transport = rmcp::transport::io::stdio();
    server.serve(transport).await.unwrap();
}
```

---

## 5. Tool Design Best Practices

### Description Engineering

The tool description is what the model reads to decide whether and how to use your tool. **This is the most important aspect of MCP tool design.**

**Bad description**:
```
"name": "search"
"description": "Search for things"
```

**Good description**:
```
"name": "search_knowledge_base"
"description": "Search the company's internal knowledge base for documentation, 
                procedures, and FAQs. Use when the user asks about company policies, 
                product features, or internal processes. Returns up to 5 relevant 
                articles with title, summary, and URL. Does NOT search the public 
                internet — use web_search for that."
```

**Description checklist**:
- [ ] What does this tool do (action + object)
- [ ] When SHOULD the model use it
- [ ] When should the model NOT use it (negative constraints reduce hallucinated calls)
- [ ] What does it return (format + content)
- [ ] Any important limitations or caveats

### Fail Loudly

```python
@mcp.tool()
async def create_order(product_id: str, quantity: int) -> str:
    if quantity <= 0:
        raise ValueError(f"Quantity must be positive, got {quantity}")
    
    product = await db.get_product(product_id)
    if not product:
        raise ValueError(f"Product '{product_id}' not found. Use list_products to see available products.")
    
    if product.stock < quantity:
        raise ValueError(
            f"Insufficient stock: {product.stock} available, {quantity} requested. "
            f"Use check_inventory to see all availability."
        )
    
    # Happy path
    order = await db.create_order(product_id, quantity)
    return f"Order {order.id} created successfully for {quantity}x {product.name}"
```

**Never return empty string or vague errors** — the model can't recover. Give specific, actionable error messages.

### Idempotency

Tools that create side effects should be idempotent:
```python
@mcp.tool()
async def send_email(to: str, subject: str, body: str, idempotency_key: str) -> str:
    """Send an email. Provide an idempotency_key to prevent duplicate sends."""
    if await email_log.exists(idempotency_key):
        return f"Email already sent (idempotency_key={idempotency_key})"
    
    await smtp.send(to=to, subject=subject, body=body)
    await email_log.record(idempotency_key, to=to, subject=subject)
    return f"Email sent to {to}"
```

---

## 6. Security & Authentication

### OAuth 2.1 for Remote MCP Servers

Remote MCP servers requiring user authorization must implement **OAuth 2.1** with:
- **PKCE (Proof Key for Code Exchange)**: Mandatory for all flows
- **Authorization Code Flow**: Standard user authentication
- **Implicit Flow**: Prohibited (security vulnerability)

```
OAuth 2.1 Flow for MCP:
1. MCP client discovers authorization server via /.well-known/oauth-authorization-server
2. Client generates code_verifier + code_challenge (PKCE)
3. Client redirects user to authorization endpoint
4. User authenticates + grants consent
5. Authorization server returns authorization code
6. Client exchanges code + code_verifier for access token
7. Client includes Bearer token in MCP requests
```

**Protected Resource Metadata** (RFC 9728): MCP servers advertise their OAuth requirements via:
```
GET https://mcp-server.example.com/.well-known/oauth-protected-resource

{
  "resource": "https://mcp-server.example.com",
  "authorization_servers": ["https://auth.example.com"],
  "scopes_supported": ["read:data", "write:orders"],
  "bearer_methods_supported": ["header"]
}
```

### Security Threats & Mitigations

| Threat | Attack Vector | Mitigation |
|---|---|---|
| **Tool Poisoning** | Malicious tool description contains hidden instructions | Trust only signed, reviewed tools; separate trusted/untrusted tool channels |
| **Prompt Injection** | Tool result contains adversarial instructions | Parse tool output as data, not instructions; structured output validation |
| **Token Theft** | Steal OAuth tokens from client storage | Short token lifetime; secure storage; token binding |
| **Confused Deputy** | Server tricked into using its permissions for unintended actions | Validate caller identity; minimal tool scope |
| **Supply Chain** | Install malicious MCP server | Use signed packages; official registry only; hash verification |
| **Rug Pull** | Tool behavior changes after deployment | Pin server versions; hash-verify server code |
| **SSRF** | Tool fetches attacker-controlled URLs | URL allowlisting; server-side SSRF protection |

---

## 7. Deployment Patterns

### Docker Containerization

```dockerfile
FROM python:3.12-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# stdio mode (for Claude Desktop)
CMD ["python", "server.py"]

# HTTP mode (override with docker run -e MODE=http)
# CMD ["python", "server.py", "--http"]
```

```yaml
# docker-compose.yml for development
version: "3.8"
services:
  mcp-server:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    command: python server.py --http
```

### Cloudflare Workers with McpAgent

```typescript
import { McpAgent } from "agents/mcp";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

export class MyMCPServer extends McpAgent {
  server = new McpServer({ name: "cloudflare-mcp", version: "1.0.0" });

  async init() {
    this.server.tool(
      "get_kv_value",
      { key: z.string() },
      async ({ key }) => {
        const value = await this.env.KV.get(key);
        return { content: [{ type: "text", text: value ?? "not found" }] };
      }
    );
  }
}

export default {
  fetch: MyMCPServer.mount("/mcp"),
};
```

**Cloudflare Durable Objects**: Each McpAgent instance is a Durable Object — stateful, globally distributed, handles SSE connections and state persistence across requests.

### Claude Desktop Configuration

```json
// ~/Library/Application Support/Claude/claude_desktop_config.json (macOS)
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/username/Documents"],
      "env": {}
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..."
      }
    },
    "my-custom-server": {
      "command": "python",
      "args": ["/path/to/my-server.py"],
      "env": {
        "DATABASE_URL": "postgresql://..."
      }
    },
    "remote-server": {
      "url": "https://my-mcp-server.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${MY_TOKEN}"
      }
    }
  }
}
```

---

## 8. MCP vs. Alternatives

| Feature | MCP | OpenAI Function Calling | LangChain Tools |
|---|---|---|---|
| **Standard** | Open standard (AAIF/Linux Foundation) | Proprietary to OpenAI | Framework-specific |
| **Model support** | Claude, GPT-4o, Gemini, open-source | GPT family + some others | Any (abstraction layer) |
| **Server/Client separation** | Yes (true client-server) | No (function runs in client code) | No (tools defined in code) |
| **Transport** | stdio, Streamable HTTP | API call only | In-process |
| **Authentication** | OAuth 2.1 standard | API key | Framework-dependent |
| **Discovery** | Registry + well-known endpoint | Hardcoded in system prompt | Hardcoded in code |
| **Sampling** | Yes (server-initiated LLM calls) | No | No |
| **Resources** | Yes (data without tool call) | No | Limited |
| **Streaming** | Yes (native SSE support) | Yes (stream flag) | Varies |
| **Ecosystem** | 9,400+ servers | Limited | Rich but framework-locked |

---

## 9. MCP Ecosystem & Discovery

### Official & Community Registries

- **MCP Registry** (official): `registry.modelcontextprotocol.io` — verified, curated servers
- **Smithery** (`smithery.ai`): Search, install, and compose MCP servers
- **mcp.so**: Directory with categories and ratings
- **PulseMCP**: Newsletter + trending servers

### Popular Canonical MCP Servers (Reference Implementations)

| Server | Function | Install |
|---|---|---|
| `@modelcontextprotocol/server-filesystem` | Read/write local files | `npx -y @mcp/server-filesystem` |
| `@modelcontextprotocol/server-github` | GitHub API (repos, PRs, issues) | `npx -y @mcp/server-github` |
| `@modelcontextprotocol/server-postgres` | PostgreSQL queries | `npx -y @mcp/server-postgres` |
| `@modelcontextprotocol/server-brave-search` | Brave web search | `npx -y @mcp/server-brave-search` |
| `@modelcontextprotocol/server-slack` | Slack messages + channels | `npx -y @mcp/server-slack` |
| `@modelcontextprotocol/server-puppeteer` | Browser automation | `npx -y @mcp/server-puppeteer` |
| `mcp-server-sqlite` | SQLite queries | `uvx mcp-server-sqlite` |

### MCP Business Models

- **Usage-based**: Charge per API call or per tool invocation
- **Subscription**: Monthly access to MCP server capabilities (21st.dev achieved $10K MRR in 6 weeks)
- **Marketplace listing**: Smithery/mcp.so charge for featured placement
- **Enterprise licensing**: Private MCP servers with SLA, support, custom tools

---

## 10. Multi-Agent Patterns with MCP

### MCP as Tool Layer; A2A as Agent Layer

```
┌─────────────────────────────────────────────┐
│           Multi-Agent Orchestrator           │
│  (Supervisor agent with A2A communication)  │
└─────────┬────────────────────┬──────────────┘
          │ A2A Protocol        │ A2A Protocol
┌─────────▼──────┐    ┌────────▼───────────────┐
│  Research Agent │    │   Writing Agent        │
│  (Claude)       │    │   (GPT-4o)             │
│                 │    │                        │
│  MCP Client     │    │  MCP Client            │
└───────┬─────────┘    └──────┬─────────────────┘
        │ MCP                  │ MCP
┌───────▼──────┐    ┌──────────▼────────┐
│ Web Search   │    │  Document Store    │
│ MCP Server  │    │  MCP Server        │
└──────────────┘    └───────────────────┘
```

**Architecture principle**: MCP handles the tool/data layer (structured, capability-exposing). A2A (Agent-to-Agent protocol) handles agent-to-agent task delegation, structured task cards, streaming progress updates.

### Composing MCP Servers

```python
# Proxy server: aggregate multiple MCP servers behind one endpoint
from fastmcp import FastMCP
from fastmcp.client import Client

proxy = FastMCP("aggregator")

# Mount sub-servers
@proxy.tool()
async def web_search(query: str) -> str:
    async with Client("npx -y @mcp/server-brave-search") as client:
        result = await client.call_tool("brave_web_search", {"query": query})
        return result[0].text

@proxy.tool()
async def read_file(path: str) -> str:
    async with Client("npx -y @mcp/server-filesystem /home/user") as client:
        result = await client.call_tool("read_file", {"path": path})
        return result[0].text
```

### Version History

| Date | Version | Key Change |
|---|---|---|
| Nov 25, 2024 | 0.1 | Initial release; stdio + SSE; Tools, Resources, Prompts |
| Jan 2025 | 0.5 | Sampling primitive added |
| Mar 2025 | 1.0 | Streamable HTTP replaces SSE; Roots added |
| Jun 2025 | 1.1 | Elicitation added; tool annotations; OAuth 2.1 formalized |
| Dec 2025 | 1.1 + AAIF | Donated to Linux Foundation AAIF; OpenAI/Google/Microsoft join |
| Mar 2026 | 1.2 | A2A interop guidance; enhanced resource subscriptions |
