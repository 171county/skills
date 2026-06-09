---
name: mcp-expert
description: Expert-level knowledge of the Model Context Protocol (MCP)—architecture, JSON-RPC 2.0 protocol, all primitives (Tools, Resources, Prompts, Sampling, Elicitation), transport layers (stdio, Streamable HTTP), authentication (OAuth 2.1 with PKCE), building servers in Python (FastMCP) and TypeScript, MCP client implementation, tool design best practices, testing, production deployment, and ecosystem. Use for any task involving building, debugging, configuring, or reasoning about MCP servers and clients.
---

# MCP Expert

## What MCP Is

The **Model Context Protocol** is an open standard from Anthropic (November 2024) defining how AI applications connect LLMs to external data sources and tools through a uniform protocol layer. Think of it as HTTP for AI tool connectivity—any client speaks to any server without prior coordination.

**Status:** 10,000+ public MCP servers across GitHub, npm, PyPI, Smithery, Glama, PulseMCP. 300+ client applications including Claude Desktop, Cursor, Windsurf, VS Code Copilot, Zed.

---

## Core Architecture

### The Three-Role Model

```
┌─────────────────────────────────────────────────────────┐
│                        HOST                             │
│  (Claude Desktop, Cursor, VS Code, custom application)  │
│                                                         │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐         │
│   │ MCP      │    │ MCP      │    │ MCP      │         │
│   │ Client 1 │    │ Client 2 │    │ Client 3 │         │
│   └────┬─────┘    └────┬─────┘    └────┬─────┘         │
└────────┼──────────────┼──────────────┼──────────────────┘
         │              │              │  (1:1 stateful JSON-RPC sessions)
    ┌────▼─────┐   ┌────▼─────┐  ┌────▼─────┐
    │ Server A │   │ Server B │  │ Server C │
    │(filesystem│  │(GitHub)  │  │(Postgres)│
    └──────────┘   └──────────┘  └──────────┘
```

**Hosts** — top-level AI applications; create and manage MCP client instances; control which servers connect; maintain the trust boundary.

**Clients** — protocol handlers inside the host; each maintains exactly one stateful JSON-RPC session with one server; proxy server capabilities to the LLM.

**Servers** — independent processes (or network services) exposing Tools, Resources, and/or Prompts. Single-purpose. Can be any language. Servers do NOT communicate directly with each other.

**Key design principles:**
- Isolation: Each server runs in its own process
- Stateful sessions: Capability negotiation happens once at connection time
- Bidirectionality: Servers can request LLM completions from clients (Sampling)
- Transport agnostic: Same JSON-RPC message format over stdio or HTTP

---

## JSON-RPC 2.0 Protocol

Every message is JSON-RPC 2.0:

**Request:**
```json
{"jsonrpc": "2.0", "id": 1, "method": "tools/call",
 "params": {"name": "search", "arguments": {"q": "MCP"}}}
```

**Response:**
```json
{"jsonrpc": "2.0", "id": 1,
 "result": {"content": [{"type": "text", "text": "Results..."}]}}
```

**Error response:**
```json
{"jsonrpc": "2.0", "id": 1,
 "error": {"code": -32602, "message": "Invalid params", "data": {"field": "q"}}}
```

**Notification (fire-and-forget, no id):**
```json
{"jsonrpc": "2.0", "method": "notifications/tools/list_changed"}
```

**Standard MCP error codes:**
- `-32700` Parse error, `-32600` Invalid Request, `-32601` Method not found
- `-32602` Invalid params, `-32603` Internal error
- `-32001` Request cancelled, `-32002` Content too large (MCP-specific)

### Complete Methods Reference

| Method | Direction | Description |
|--------|-----------|-------------|
| `initialize` | Client → Server | Handshake + capability negotiation |
| `ping` | Either → Either | Health check |
| `tools/list` | Client → Server | List available tools (paginated) |
| `tools/call` | Client → Server | Execute a tool |
| `resources/list` | Client → Server | List available resources |
| `resources/read` | Client → Server | Read resource content |
| `resources/subscribe` | Client → Server | Subscribe to resource updates |
| `resources/templates/list` | Client → Server | List URI templates |
| `prompts/list` | Client → Server | List available prompts |
| `prompts/get` | Client → Server | Retrieve a rendered prompt |
| `sampling/createMessage` | Server → Client | Request LLM completion |
| `completion/complete` | Client → Server | Request argument completion |
| `logging/setLevel` | Client → Server | Set server log level |
| `roots/list` | Server → Client | List client workspace roots |
| `elicitation/create` | Server → Client | Request user input |

---

## Capability Negotiation and Lifecycle

### Initialization Handshake

No other methods can be called until initialization completes.

**Client sends `initialize`:**
```json
{
  "jsonrpc": "2.0", "id": 1, "method": "initialize",
  "params": {
    "protocolVersion": "2025-03-26",
    "capabilities": {"roots": {"listChanged": true}, "sampling": {}},
    "clientInfo": {"name": "claude-desktop", "version": "1.5.0"}
  }
}
```

**Server responds:**
```json
{
  "jsonrpc": "2.0", "id": 1,
  "result": {
    "protocolVersion": "2025-03-26",
    "capabilities": {
      "tools": {"listChanged": true},
      "resources": {"subscribe": true, "listChanged": true},
      "prompts": {"listChanged": true},
      "logging": {}
    },
    "serverInfo": {"name": "my-server", "version": "1.0.0"}
  }
}
```

**Client sends `notifications/initialized`** — no response expected. Session is now active.

### Capability Matrix

| Capability | Server Advertises | Client Advertises |
|------------|------------------|------------------|
| `tools` | Has tools; `listChanged` = sends notifications | — |
| `resources` | Has resources; `subscribe` = supports subscriptions | — |
| `prompts` | Has prompts; `listChanged` = sends notifications | — |
| `logging` | Accepts log level changes | — |
| `sampling` | — | Can fulfill LLM requests from server |
| `roots` | — | `listChanged` = notifies on workspace changes |

---

## MCP Primitives

### Tools — Model-Controlled Actions

The LLM decides to call tools based on context and descriptions. Primary mechanism for AI-driven action.

**Tool definition:**
```json
{
  "name": "search_docs",
  "description": "Search product documentation. Returns titles, URLs, excerpts. Use when user asks how something works or needs reference material. Do NOT use for order lookups.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {"type": "string", "description": "Natural language search"},
      "max_results": {"type": "integer", "default": 5}
    },
    "required": ["query"]
  },
  "annotations": {
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": true,
    "openWorldHint": false
  }
}
```

**Tool result:**
```json
{
  "content": [
    {"type": "text", "text": "Found 3 results..."},
    {"type": "image", "data": "<base64>", "mimeType": "image/png"},
    {"type": "resource", "resource": {"uri": "file:///doc.md", "text": "..."}}
  ],
  "isError": false
}
```

When `isError: true`, the LLM sees the error and can adapt—unlike a protocol-level error.

**Tool annotations:**

| Annotation | Default | Meaning |
|------------|---------|---------|
| `readOnlyHint` | `false` | Never modifies state |
| `destructiveHint` | `true` | May permanently destroy data |
| `idempotentHint` | `false` | Calling N times = calling once |
| `openWorldHint` | `true` | Interacts with external entities |

Annotations are UX hints, NOT security controls.

---

### Resources — Application-Controlled Data

Resources are read-only data sources. The host decides when/how to surface them to the LLM.

**URI schemes:** `file:///path/to/file.txt`, `postgres://host/db/tables/users`, `github://owner/repo/issues/123`, `config://app/settings`

**Static resource + read:**
```json
// List: {"uri": "config://settings", "name": "App Config", "mimeType": "application/json"}
// Read request: {"method": "resources/read", "params": {"uri": "config://settings"}}
// Response: {"contents": [{"uri": "...", "mimeType": "application/json", "text": "{...}"}]}
```

**Resource templates** (RFC 6570 URI templates):
```json
{"uriTemplate": "github://{owner}/{repo}/issues/{number}", "name": "GitHub Issue", "mimeType": "text/markdown"}
```

**Subscriptions for live data:**
```json
// Subscribe: {"method": "resources/subscribe", "params": {"uri": "metrics://cpu"}}
// Server notifies: {"method": "notifications/resources/updated", "params": {"uri": "metrics://cpu"}}
// Client re-reads the resource
```

---

### Prompts — User-Controlled Templates

Prompts appear as slash commands or explicit UI elements. The **user** invokes them, unlike tools which the LLM invokes.

```json
{
  "name": "analyze-code",
  "description": "Comprehensive code review",
  "arguments": [
    {"name": "code", "description": "Code to analyze", "required": true},
    {"name": "language", "required": false}
  ]
}
```

**GetPromptResult** returns rendered messages ready to inject into the conversation.

---

### Sampling — Server Requests LLM Completions

Sampling allows servers to request LLM inference from the client during tool execution. Enables agentic multi-step workflows without the server needing direct LLM API access.

**Critical security requirement:** Clients MUST present sampling requests to users for approval—prevents server-side prompt injection.

```json
{
  "method": "sampling/createMessage",
  "params": {
    "messages": [{"role": "user", "content": {"type": "text", "text": "Classify: 'Amazing product!'"}}],
    "modelPreferences": {
      "hints": [{"name": "claude-haiku-4-5"}],
      "costPriority": 0.8,
      "speedPriority": 0.9,
      "intelligencePriority": 0.3
    },
    "systemPrompt": "Reply with only: POSITIVE, NEGATIVE, or NEUTRAL.",
    "includeContext": "none",
    "maxTokens": 10
  }
}
```

`includeContext`: `"none"` | `"thisServer"` | `"allServers"`

---

### Elicitation — Request User Input from Server

Servers request structured or URL-based input from users mid-execution:

```python
# Structured form
response = await ctx.elicit(
    message="Confirm production deployment?",
    schema={"type": "object", "properties": {"confirmed": {"type": "boolean"}}, "required": ["confirmed"]}
)
if response.action == "cancel" or not response.data.get("confirmed"):
    return "Cancelled"

# URL-based (OAuth flows)
await ctx.elicit_url(message="Authenticate with external service", url=auth_url, elicitation_id="oauth")
```

---

## Transport Layer

### stdio Transport (Local Servers)

Host spawns server as child process. Communication over stdin/stdout with newline-delimited JSON. Stderr for logs.

```
Host Process                   Server Process
     │  spawn(command, args, env)    │
     │────────────────────────────── │
     │  stdin → JSON-RPC requests   │
     │  stdout ← JSON-RPC responses │
     │  stderr ← logs (out of band) │
```

No network needed. OS process isolation. API keys via environment variables. No OAuth required.

**Use for:** Local development tools, filesystem operations, local databases.

### Streamable HTTP Transport (Remote Servers)

Single `/mcp` endpoint (spec `2025-03-26`, replaces legacy HTTP+SSE):
- **POST /mcp** — Client sends requests; server responds with JSON or SSE stream
- **GET /mcp** — Client opens SSE stream for server-initiated messages (sampling, notifications)
- **DELETE /mcp** — Client terminates session

`Mcp-Session-Id` header ties requests to a session. For stateless deployments: `stateless_http=True`.

**Resumability:** Servers can provide `EventStore`. Clients reconnect with `Last-Event-ID` for missed event replay—critical for long-running agentic tasks.

**Why SSE was deprecated:** Dual endpoints (GET /sse + POST /messages) with coordinated state are error-prone and fail on load balancers. Streamable HTTP unifies to a single endpoint. SSE fully removed from spec 2025-06-18.

**Transport security:**

| Transport | TLS | Auth |
|-----------|-----|------|
| stdio | N/A (OS isolation) | env vars |
| Streamable HTTP (prod) | HTTPS + TLS 1.3 | OAuth 2.1 + PKCE |
| Streamable HTTP (localhost) | Not required | Token or none |

---

## Building Servers in Python (FastMCP)

```bash
# Install
uv add "mcp[cli]"   # Official SDK (includes FastMCP)
pip install fastmcp  # FastMCP 2.0 standalone (more features)
```

### Complete Server Example

```python
from contextlib import asynccontextmanager
from dataclasses import dataclass
from typing import AsyncIterator
import httpx
from mcp.server.fastmcp import FastMCP, Context

@dataclass
class AppContext:
    http_client: httpx.AsyncClient
    api_key: str

@asynccontextmanager
async def app_lifespan(server: FastMCP) -> AsyncIterator[AppContext]:
    async with httpx.AsyncClient() as client:
        yield AppContext(http_client=client, api_key=server.settings.env.get("API_KEY", ""))

mcp = FastMCP("Weather Server", instructions="Provides weather data.", lifespan=app_lifespan)

@mcp.tool()
async def get_weather(city: str, units: str = "celsius", ctx: Context = None) -> dict:
    """
    Get current weather for a city. Returns temperature, humidity, wind speed, conditions.
    Use when user asks about current weather or temperature in a specific location.
    Do NOT use for historical weather queries.
    
    Args:
        city: City name, optionally with country code (e.g., 'London, UK')
        units: 'celsius' or 'fahrenheit'
    """
    app_ctx: AppContext = ctx.request_context.lifespan_context
    await ctx.report_progress(progress=0.0, total=1.0, message="Fetching weather")
    response = await app_ctx.http_client.get(
        "https://api.weather.example.com/current",
        params={"city": city, "units": units},
        headers={"Authorization": f"Bearer {app_ctx.api_key}"}
    )
    response.raise_for_status()
    await ctx.report_progress(progress=1.0, total=1.0, message="Done")
    return response.json()

@mcp.resource("weather://current/{city}")
async def weather_resource(city: str) -> str:
    """Current weather data as a formatted text report."""
    return f"Weather report for {city}: ..."

@mcp.prompt(title="Weather Analysis")
def weather_prompt(city: str, focus: str = "general") -> str:
    """Generate a weather analysis prompt. Args: city, focus (general/travel/agriculture)."""
    focus_map = {"travel": "Focus on travel conditions.", "agriculture": "Focus on growing conditions."}
    return f"Analyze current weather for {city}. {focus_map.get(focus, 'General overview.')} Include trends, precipitation, and notable events in next 48 hours."

if __name__ == "__main__":
    mcp.run()  # stdio (default)
    # mcp.run(transport="streamable-http", host="0.0.0.0", port=8000)
```

### Structured Output with Pydantic

```python
from pydantic import BaseModel

class WeatherData(BaseModel):
    temperature: float
    humidity: float
    condition: str
    wind_speed_kmh: float

@mcp.tool()
def get_weather_structured(city: str) -> WeatherData:
    """Get structured weather data."""
    return WeatherData(temperature=22.5, humidity=65.0, condition="partly cloudy", wind_speed_kmh=15.0)
```

Returns both `content` (text for backward compat) and `structuredContent` (validated JSON).

### FastMCP Decorator Reference

```python
# Tool
@mcp.tool(name="override", title="Display Name", annotations={"readOnlyHint": True})
def my_tool(param: str, ctx: Context = None) -> ReturnType: ...

# Resource (static)
@mcp.resource("scheme://path")
def my_resource() -> str | dict | bytes: ...

# Resource (template)
@mcp.resource("scheme://{param1}/{param2}")
def my_template(param1: str, param2: str) -> str: ...

# Prompt
@mcp.prompt(title="Human Name")
def my_prompt(arg1: str, arg2: str = "default") -> str | list[Message]: ...
```

### Install and Dev Tools

```bash
# Install to Claude Desktop config
uv run mcp install server.py --name "My Server" -v API_KEY=secret

# Development mode with hot reload + Inspector
uv run mcp dev server.py --with httpx
```

---

## Building Servers in TypeScript

```bash
npm install @modelcontextprotocol/server
```

```typescript
import { McpServer, ResourceTemplate } from "@modelcontextprotocol/server";
import { StdioServerTransport } from "@modelcontextprotocol/server/stdio";
import * as z from "zod/v4";

const server = new McpServer({ name: "code-server", version: "1.0.0" });

server.registerTool("analyze", {
  description: "Analyze code complexity. Returns score, rating, suggestions. Use for code quality review.",
  inputSchema: z.object({
    code: z.string().describe("Function source code"),
    language: z.enum(["python", "typescript"]).default("typescript")
  }),
  outputSchema: z.object({
    complexity: z.number(),
    rating: z.enum(["low", "medium", "high"]),
    suggestions: z.array(z.string())
  }),
  annotations: { readOnlyHint: true, idempotentHint: true }
}, async ({ code, language }) => {
  const result = { complexity: 5, rating: "low" as const, suggestions: [] };
  return { content: [{ type: "text", text: JSON.stringify(result) }], structuredContent: result };
});

server.registerResource("config", "config://project",
  { title: "Project Config", mimeType: "application/json" },
  async (uri) => ({ contents: [{ uri: uri.href, mimeType: "application/json", text: '{"version":"1.0"}' }] })
);

server.registerPrompt("review", {
  title: "Code Review",
  argsSchema: z.object({ code: z.string(), focus: z.enum(["security", "performance", "all"]).default("all") })
}, ({ code, focus }) => ({
  messages: [{ role: "user", content: { type: "text", text: `Review this code for ${focus}:\n\`\`\`\n${code}\n\`\`\`` } }]
}));

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}
main().catch(console.error);
```

### Streamable HTTP (TypeScript Production)

```typescript
import express from "express";
import { NodeStreamableHTTPServerTransport } from "@modelcontextprotocol/server/node";
import { randomUUID } from "crypto";

const app = express();
app.use(express.json());
const transports = new Map();

app.all("/mcp", async (req, res) => {
  const sessionId = req.headers["mcp-session-id"] as string;
  let transport = sessionId && transports.get(sessionId);
  
  if (!transport) {
    transport = new NodeStreamableHTTPServerTransport({
      sessionIdGenerator: () => randomUUID(),
      onsessioninitialized: (id) => transports.set(id, transport)
    });
    const sessionServer = createServer();
    await sessionServer.connect(transport);
  }
  await transport.handleRequest(req, res);
});

app.listen(3000);
```

---

## MCP Client Implementation

### Python Client

```python
import asyncio
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def main():
    params = StdioServerParameters(command="python", args=["server.py"], env={"API_KEY": "secret"})
    async with stdio_client(params) as (read, write):
        async with ClientSession(read, write) as session:
            await session.initialize()
            tools = await session.list_tools()
            result = await session.call_tool("get_weather", {"city": "London"})
            resource = await session.read_resource("config://settings")
            prompt = await session.get_prompt("weather_prompt", {"city": "London"})

asyncio.run(main())
```

### TypeScript Client

```typescript
import { Client } from "@modelcontextprotocol/client";
import { StdioClientTransport } from "@modelcontextprotocol/client/stdio";

const client = new Client({ name: "my-client", version: "1.0.0" });
const transport = new StdioClientTransport({ command: "node", args: ["build/index.js"] });
await client.connect(transport);

const { tools } = await client.listTools();
const result = await client.callTool({ name: "analyze", arguments: { code: "..." } });
const resource = await client.readResource({ uri: "config://project" });
await client.close();
```

---

## Tool Design Best Practices

### Description Quality Is Everything

The LLM's ability to select and use your tool correctly depends almost entirely on your description.

**Bad:**
```python
@mcp.tool()
def search(q: str) -> list:
    """Search."""
```

**Good:**
```python
@mcp.tool()
def search_products(query: str, category: str | None = None, in_stock_only: bool = False) -> list[dict]:
    """
    Search the product catalog for items matching given criteria.
    Returns list of products with name, SKU, price, stock status, description.
    Use when user asks to find products, search catalog, or browse items.
    Do NOT use for order lookup or account queries.
    
    Args:
        query: Natural language search (e.g., 'blue running shoes size 10')
        category: Optional filter (e.g., 'footwear', 'electronics')
        in_stock_only: If True, only return in-stock items
    """
```

**Description checklist:**
- [ ] What does this do? (one sentence)
- [ ] What does it return? (format and fields)
- [ ] When SHOULD the LLM use it?
- [ ] When should it NOT use it?
- [ ] Each parameter described with type, format, example values
- [ ] Any limits or constraints documented

**Naming:** `verb_noun` snake_case—`get_user_profile`, `list_open_orders`, `create_ticket`, `search_knowledge_base`. Avoid: `process`, `handle`, `do` (too vague).

### Error Handling

```python
@mcp.tool()
async def risky_op(input: str, ctx: Context) -> str:
    try:
        return await do_work(input)
    except ValueError as e:
        from mcp.types import CallToolResult, TextContent
        return CallToolResult(content=[TextContent(type="text", text=f"Invalid input: {e}")], isError=True)
    except Exception as e:
        await ctx.error(f"Unexpected error: {e}")
        return CallToolResult(content=[TextContent(type="text", text="Internal error. Please try again.")], isError=True)
```

### Security

**Path traversal prevention:**
```python
import os
def safe_read(user_path: str, base_dir: str) -> str:
    resolved = os.path.realpath(os.path.join(base_dir, user_path))
    if not resolved.startswith(os.path.realpath(base_dir) + os.sep):
        raise ValueError("Path traversal attempt detected")
    with open(resolved) as f:
        return f.read()
```

**Rate limiting:**
```python
from collections import defaultdict
from time import time

call_counts = defaultdict(list)

@mcp.tool()
async def expensive_tool(input: str, ctx: Context) -> str:
    now = time()
    client_id = ctx.client_id or "anonymous"
    call_counts[client_id] = [t for t in call_counts[client_id] if now - t < 60]
    if len(call_counts[client_id]) >= 10:
        raise ValueError("Rate limit: max 10 calls/minute")
    call_counts[client_id].append(now)
    return do_work(input)
```

**Key security risks:**
- **Tool poisoning:** Malicious tool descriptions influence LLM behavior — verify server sources
- **Prompt injection via resources:** Resources can embed malicious instructions — treat as untrusted data
- **DNS rebinding:** Use `allowedHosts` validation for localhost HTTP servers
- Sampling requests MUST be shown to users for approval per spec

---

## Authentication (HTTP Transport)

### OAuth 2.1 with PKCE Flow

```
Client → GET /.well-known/oauth-protected-resource → Server metadata
Client → GET /.well-known/oauth-authorization-server → Auth server discovery
Client → Authorization request (PKCE: code_challenge, resource=server_url)
Client → Token exchange (code + code_verifier) → Access token
Client → POST /mcp with Authorization: Bearer <token>
Server → Verify token → Process request
```

**Server-side token verification (Python):**
```python
from mcp.server.auth.provider import TokenVerifier, AccessToken
from mcp.server.fastmcp.server import AuthSettings

class JWTTokenVerifier(TokenVerifier):
    async def verify_token(self, token: str) -> AccessToken | None:
        try:
            payload = verify_jwt(token, PUBLIC_KEY)
            return AccessToken(token=token, client_id=payload["client_id"],
                             scopes=payload.get("scope", "").split(), expires_at=payload["exp"])
        except Exception:
            return None

mcp = FastMCP("Protected API", token_verifier=JWTTokenVerifier(),
              auth=AuthSettings(issuer_url="https://auth.example.com",
                               resource_server_url="https://mcp.example.com",
                               required_scopes=["mcp:read", "mcp:tools"]))
```

---

## Testing

### MCP Inspector

```bash
# UI mode
npx @modelcontextprotocol/inspector node build/index.js

# With env vars
npx @modelcontextprotocol/inspector -e API_KEY=secret node build/index.js

# Connecting to HTTP server
npx @modelcontextprotocol/inspector --transport streamable-http --url http://localhost:3000/mcp

# CLI mode for automation
npx @modelcontextprotocol/inspector --cli node build/index.js --method tools/list
npx @modelcontextprotocol/inspector --cli node build/index.js \
  --method tools/call --tool-name get_weather --tool-arg city=London

# Multi-server config
npx @modelcontextprotocol/inspector --config mcp.json --server my-server
```

**Config file (`mcp.json`):**
```json
{
  "mcpServers": {
    "weather": {"type": "stdio", "command": "python", "args": ["server.py"], "env": {"API_KEY": "test"}},
    "remote": {"type": "streamable-http", "url": "http://localhost:3000/mcp"}
  }
}
```

### Integration Testing (Python)

```python
import pytest
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

@pytest.mark.asyncio
async def test_tools():
    params = StdioServerParameters(command="python", args=["src/server.py"], env={"API_KEY": "test"})
    async with stdio_client(params) as (read, write):
        async with ClientSession(read, write) as session:
            await session.initialize()
            tools = await session.list_tools()
            assert "get_weather" in [t.name for t in tools.tools]
            result = await session.call_tool("get_weather", {"city": "London"})
            assert not result.isError
```

### TypeScript Testing with InMemoryTransport

```typescript
import { InMemoryTransport } from "@modelcontextprotocol/server/inMemory";

const server = createServer();
const client = new Client({ name: "test", version: "1.0" });
const [clientTransport, serverTransport] = InMemoryTransport.createLinkedPair();
await server.connect(serverTransport);
await client.connect(clientTransport);
const { tools } = await client.listTools();
```

**Log locations:**
- Claude Desktop macOS: `~/Library/Logs/Claude/mcp*.log`
- Claude Desktop Windows: `%APPDATA%\Claude\logs\mcp*.log`
- Claude Code: `~/.claude/logs/`

---

## Production Deployment

### Claude Desktop Configuration

```json
// ~/Library/Application Support/Claude/claude_desktop_config.json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx", "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
    },
    "github": {
      "command": "npx", "args": ["-y", "@github/github-mcp-server"],
      "env": {"GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxx"}
    },
    "my-python-server": {
      "command": "uv",
      "args": ["run", "--project", "/path/to/server", "python", "src/server.py"],
      "env": {"API_KEY": "secret"}
    },
    "remote-server": {
      "type": "streamable-http",
      "url": "https://my-server.example.com/mcp",
      "headers": {"Authorization": "Bearer token"}
    }
  }
}
```

### Claude Code CLI Configuration

```bash
claude mcp add my-server python /path/to/server.py
claude mcp add my-server -e API_KEY=secret -- python /path/to/server.py
claude mcp add remote --transport streamable-http https://server.example.com/mcp
claude mcp list
claude mcp remove my-server
```

Config in `~/.claude.json` (global) or `.mcp.json` (project-level).

### Docker Production Server

```dockerfile
FROM python:3.12-slim
WORKDIR /app
RUN pip install uv
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev
COPY src/ ./src/
EXPOSE 8000
CMD ["uv", "run", "python", "src/server.py"]
```

```python
# Production with health endpoint
from starlette.applications import Starlette
from starlette.responses import JSONResponse
from starlette.routing import Mount, Route

mcp = FastMCP("Server", stateless_http=True, json_response=True)

async def health(request):
    return JSONResponse({"status": "ok"})

app = Starlette(routes=[Mount("/mcp", app=mcp.streamable_http_app()), Route("/health", health)])
```

---

## Server Composition (FastMCP)

```python
weather_server = FastMCP("weather")
@weather_server.tool()
def get_weather(city: str) -> dict: ...

news_server = FastMCP("news")
@news_server.tool()
def get_headlines(topic: str) -> list[str]: ...

main = FastMCP("aggregator")
main.mount("weather", weather_server)   # tools: weather_get_weather
main.mount("news", news_server)         # tools: news_get_headlines
```

---

## Essential MCP Servers

**Install on every workstation:**

| Server | Package | Key Tools |
|--------|---------|-----------|
| Filesystem | `@modelcontextprotocol/server-filesystem` | read_file, write_file, list_directory |
| GitHub (official) | `github/github-mcp-server` | search_repos, create_issue, push_files |
| Memory | `@modelcontextprotocol/server-memory` | create_entities, search_nodes |
| Sequential Thinking | `@modelcontextprotocol/server-sequentialthinking` | think |
| Fetch | `@modelcontextprotocol/server-fetch` | fetch (web → markdown) |

**Domain-specific:**

| Server | Package | Use Case |
|--------|---------|----------|
| Postgres | `@modelcontextprotocol/server-postgres` | NL SQL queries |
| SQLite | `@modelcontextprotocol/server-sqlite` | Local DB exploration |
| Exa | `exa-labs/exa-mcp-server` | AI-powered web search |
| Slack | `@modelcontextprotocol/server-slack` | Workspace comms |

**Discovery:** [smithery.ai](https://smithery.ai), [mcp.so](https://mcp.so), [glama.ai/mcp](https://glama.ai/mcp), [pulsemcp.com](https://pulsemcp.com)

---

## Decision Tree: Which Primitive?

```
LLM should decide to do this autonomously?
  → TOOL

Read-only data the LLM should have as context?
  → RESOURCE
    Parameterized? → RESOURCE TEMPLATE
    Changes over time? → add SUBSCRIPTION support

User explicitly triggers a workflow?
  → PROMPT

Server needs LLM intelligence during tool execution?
  → SAMPLING (createMessage)

Server needs OAuth or URL-based user action?
  → ELICITATION (elicit_url)

Server needs structured form input from user?
  → ELICITATION (elicit with schema)
```

---

## Quick Reference

```
# Lifecycle
initialize → established session, capabilities negotiated
notifications/initialized → session active
ping ↔ health check

# Tools
tools/list → [{name, description, inputSchema, annotations}]
tools/call → {content[], isError, structuredContent}
notifications/tools/list_changed

# Resources
resources/list → [{uri, name, description, mimeType}]
resources/templates/list → [{uriTemplate, ...}]
resources/read → {contents: [{uri, text|blob, mimeType}]}
resources/subscribe / unsubscribe
notifications/resources/list_changed / updated

# Prompts
prompts/list → [{name, description, arguments[]}]
prompts/get → {description, messages[]}
notifications/prompts/list_changed

# Server→Client
sampling/createMessage → {messages, modelPreferences, maxTokens}
elicitation/create → {message, requestedSchema}
roots/list → {roots: [{uri, name}]}

# Utilities
logging/setLevel, completion/complete
notifications/progress, notifications/message
```
