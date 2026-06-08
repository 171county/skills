---
name: mcp-expert
description: Deep expert on the Model Context Protocol (MCP) — both as a user connecting to servers and as a builder creating them. Use when the user asks about MCP concepts, architecture, specification details, how to build MCP servers, how to configure MCP clients, best practices, security, debugging, advanced patterns, or anything related to MCP.
license: Complete terms in LICENSE.txt
---

# MCP Expert Knowledge Base

You are a deep expert on the Model Context Protocol (MCP). This document is your comprehensive reference. Answer from it authoritatively — do not hedge unnecessarily.

---

## 1. What is MCP — Overview and Design Philosophy

MCP (Model Context Protocol) is an open standard for connecting AI applications (clients/hosts) to external data sources and tools (servers) in a standardized, interoperable way. Announced by Anthropic in November 2024 and donated to the Agentic AI Foundation (under the Linux Foundation) in December 2025, it is now governed by a consortium including Anthropic, OpenAI, Google, Microsoft, AWS, and Block.

**Core problem it solves:** Before MCP, every AI app needed custom integrations for every tool and data source. MCP provides a universal connector — like USB-C for AI — so any MCP client can talk to any MCP server without bespoke glue code.

**Design philosophy:**
- Separation of concerns: context provision (server) is decoupled from LLM interaction (host/client)
- Stateful sessions with structured lifecycle
- Protocol-level security through human-in-the-loop and capability negotiation
- Provider-agnostic: works with any LLM
- Composability: servers can be combined, proxied, and chained

**Key numbers (as of mid-2026):** 97 million+ monthly SDK downloads, 10,000+ public servers, 300+ client implementations.

---

## 2. Architecture

### Three Roles

```
┌─────────────────────────────────────────────────┐
│                   MCP Host                       │
│  (Claude Desktop, Claude Code, Cursor, IDE...)   │
│                                                   │
│  ┌─────────────┐    ┌─────────────┐              │
│  │ MCP Client  │    │ MCP Client  │   (one per   │
│  │  (1:1 conn) │    │  (1:1 conn) │    server)   │
│  └──────┬──────┘    └──────┬──────┘              │
└─────────│─────────────────│───────────────────────┘
          │ JSON-RPC 2.0    │ JSON-RPC 2.0
          ▼                 ▼
   ┌────────────┐    ┌────────────┐
   │ MCP Server │    │ MCP Server │
   │ (GitHub)   │    │ (Postgres) │
   └────────────┘    └────────────┘
```

- **Host**: The AI application (Claude Desktop, Claude Code, Cursor). Manages multiple client connections and the LLM.
- **Client**: Lives inside the host. Maintains a single, isolated 1:1 connection to one server. Handles session state.
- **Server**: Exposes tools, resources, and prompts. Can be local (subprocess) or remote (HTTP service).

### MCP vs OpenAI Function Calling

| Aspect | MCP | OpenAI Function Calling |
|--------|-----|------------------------|
| Architecture | Independent server process | Functions defined in app |
| Discovery | Dynamic at runtime | Static per request |
| Portability | Provider-agnostic | OpenAI-specific format |
| Scale | Hundreds of tools without prompt bloat | Every schema sent each request |
| Transport | stdio or HTTP | Always HTTP |
| State | Stateful sessions | Stateless per-call |
| Best for | Production multi-tool systems | Simple single-provider bots |

---

## 3. JSON-RPC 2.0 Message Format

MCP uses JSON-RPC 2.0 exclusively. Three message types:

### Request (expects a response)
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": { "city": "London" }
  }
}
```

### Response (success)
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [{ "type": "text", "text": "Cloudy, 15°C" }],
    "isError": false
  }
}
```

### Response (error)
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32600,
    "message": "Invalid Request",
    "data": { "details": "..." }
  }
}
```

### Notification (no response expected)
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/tools/list_changed"
}
```

**Standard JSON-RPC error codes:**
- `-32700`: Parse error
- `-32600`: Invalid request
- `-32601`: Method not found
- `-32602`: Invalid params
- `-32603`: Internal error

---

## 4. Protocol Lifecycle

### Phase 1: Initialization

**Client sends:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-11-25",
    "clientInfo": { "name": "claude-code", "version": "2.1.0" },
    "capabilities": {
      "roots": { "listChanged": true },
      "sampling": { "tools": {} },
      "elicitation": { "form": {}, "url": {} },
      "experimental": {}
    }
  }
}
```

**Server responds:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2025-11-25",
    "serverInfo": { "name": "my-server", "version": "1.0.0" },
    "instructions": "Use search_code first, then read_file for details.",
    "capabilities": {
      "tools": { "listChanged": true },
      "resources": { "subscribe": true, "listChanged": true },
      "prompts": { "listChanged": true },
      "logging": {},
      "completions": {},
      "tasks": { "cancel": {} },
      "experimental": {}
    }
  }
}
```

**Client confirms readiness:**
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

**CRITICAL:** Only `ping` and `notifications/message` (logging) are allowed before initialization completes.

### Phase 2: Operation

After initialization, the client and server exchange requests, responses, and notifications freely based on negotiated capabilities.

### Phase 3: Shutdown

No special protocol messages. The client closes the transport connection.

---

## 5. Complete Method Reference (Spec 2025-11-25)

### Client → Server Requests

| Method | Description |
|--------|-------------|
| `initialize` | Start session, exchange capabilities |
| `ping` | Health check |
| `tools/list` | Discover available tools (paginated) |
| `tools/call` | Invoke a tool |
| `resources/list` | List available resources (paginated) |
| `resources/templates/list` | List URI templates |
| `resources/read` | Read a resource by URI |
| `resources/subscribe` | Subscribe to resource updates |
| `resources/unsubscribe` | Cancel subscription |
| `prompts/list` | List available prompts (paginated) |
| `prompts/get` | Retrieve a prompt with arguments |
| `completion/complete` | Request argument autocomplete |
| `logging/setLevel` | Set server log level |
| `roots/list` | Request client's workspace roots |
| `tasks/list` | List tasks (experimental) |
| `tasks/get` | Get task status (experimental) |
| `tasks/result` | Get task result (experimental) |
| `tasks/cancel` | Cancel a task (experimental) |

### Server → Client Requests

| Method | Description |
|--------|-------------|
| `sampling/createMessage` | Request LLM completion from client |
| `elicitation/request` | Request user input (form or URL mode) |

### Notifications (either direction)

| Method | Direction | Description |
|--------|-----------|-------------|
| `notifications/initialized` | Client→Server | Session ready |
| `notifications/cancelled` | Either | Cancel a pending request |
| `notifications/progress` | Server→Client | Progress update |
| `notifications/message` | Server→Client | Log message |
| `notifications/tools/list_changed` | Server→Client | Tool list updated |
| `notifications/resources/list_changed` | Server→Client | Resource list updated |
| `notifications/resources/updated` | Server→Client | Specific resource changed |
| `notifications/prompts/list_changed` | Server→Client | Prompt list updated |
| `notifications/roots/list_changed` | Client→Server | Workspace roots changed |
| `notifications/task/status` | Server→Client | Task status update |

---

## 6. Transport Layers

### stdio (Standard Input/Output)

**How it works:** Server runs as a subprocess. Client writes newline-delimited JSON-RPC to stdin, reads from stdout. Stderr is reserved for logging.

**Use when:**
- Local development
- Claude Desktop integration
- Single-user, single-client tools
- Tools that need direct filesystem/OS access

**Critical rule:** For stdio servers, NEVER write to stdout (not even with `console.log` in JS). All logging must go to stderr. Writing non-JSON-RPC data to stdout corrupts the protocol.

**Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS, `%APPDATA%\Claude\claude_desktop_config.json` on Windows):**
```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["/path/to/server/dist/index.js"],
      "env": {
        "API_KEY": "your-key-here"
      }
    }
  }
}
```

### Streamable HTTP (Current Standard for Remote Servers)

**How it works:** Introduced in spec 2025-03-26. Single endpoint supporting both POST and GET. Client POSTs requests; server may respond with plain JSON or upgrade to SSE for streaming. Session ID (`MCP-Session-Id` header) optionally maintains state.

**Endpoint behavior:**
- `POST /mcp` — client sends JSON-RPC request; server returns JSON or `text/event-stream` SSE
- `GET /mcp` — client opens SSE stream for server-initiated messages

**Stateless mode:** No `sessionIdGenerator`, each request is independent. Deploy behind load balancers freely.

**Stateful mode:** Server generates a cryptographically random session ID returned in `MCP-Session-Id` response header during initialization. Client includes it in all subsequent requests.

**Use when:**
- Remote/cloud deployment
- Multiple clients connecting to one server
- Production services requiring scalability

**Default URL:** `http://localhost:8000/mcp`

### SSE (Server-Sent Events) — DEPRECATED

The original HTTP transport (spec pre-2025-03-26). Two separate endpoints (`/sse` and `/messages`). Deprecated in favor of Streamable HTTP. Still supported for backward compatibility but should not be used for new servers.

### WebSocket

Supported by Claude Code for bidirectional real-time connections. Use when a server needs to push events proactively (e.g., CI results, monitoring alerts). Does not support OAuth.

### Transport Selection Summary

| Criterion | stdio | Streamable HTTP | WebSocket |
|-----------|-------|-----------------|-----------|
| Deployment | Local only | Remote or local | Remote |
| Multiple clients | No | Yes | Yes |
| OAuth support | No | Yes | No |
| Push events | No | Via SSE | Yes (native) |
| Complexity | Low | Medium | Medium |
| Recommended for | Dev, local tools | All production | Event push |

---

## 7. Core Primitives

### 7.1 Tools

Tools are the primary primitive — model-controlled functions the LLM can invoke to perform actions with side effects.

**Full tool definition (spec 2025-11-25):**
```json
{
  "name": "github_create_issue",
  "title": "Create GitHub Issue",
  "description": "Creates a new issue in a GitHub repository. Use when the user asks to file a bug report or feature request. Returns the issue number and URL.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "owner": {
        "type": "string",
        "description": "Repository owner (username or org name, e.g., 'octocat')"
      },
      "repo": {
        "type": "string",
        "description": "Repository name (e.g., 'hello-world')"
      },
      "title": {
        "type": "string",
        "description": "Issue title (1-256 chars)"
      },
      "body": {
        "type": "string",
        "description": "Issue body in Markdown format"
      }
    },
    "required": ["owner", "repo", "title"]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "number": { "type": "integer" },
      "url": { "type": "string" },
      "id": { "type": "integer" }
    }
  },
  "annotations": {
    "title": "Create GitHub Issue",
    "readOnlyHint": false,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "execution": {
    "taskSupport": "optional"
  }
}
```

**Tool result format:**
```json
{
  "content": [
    { "type": "text", "text": "Created issue #42: https://github.com/..." }
  ],
  "structuredContent": {
    "number": 42,
    "url": "https://github.com/octocat/hello-world/issues/42",
    "id": 12345678
  },
  "isError": false
}
```

**Key rule:** Tool errors MUST be reported with `isError: true` inside the result object, NOT as JSON-RPC protocol errors. This lets the LLM see the error and self-correct. Only use protocol errors for cases where the tool itself doesn't exist or the server doesn't support tool calls.

**Content types in tool results:**
- `{ "type": "text", "text": "..." }` — plain text
- `{ "type": "image", "data": "<base64>", "mimeType": "image/png" }` — images
- `{ "type": "audio", "data": "<base64>", "mimeType": "audio/wav" }` — audio
- `{ "type": "resource", ... }` — embedded resource
- `{ "type": "resource_link", "uri": "...", "name": "...", "description": "..." }` — reference to resource

### 7.2 Tool Annotations (Hints)

All four are hints — not guarantees. Clients must not make security-critical decisions based on annotations from untrusted servers.

| Annotation | Default | Meaning |
|------------|---------|---------|
| `readOnlyHint` | `false` | Tool does not modify its environment |
| `destructiveHint` | `true` | Tool may perform destructive updates (meaningful only when `readOnlyHint=false`) |
| `idempotentHint` | `false` | Repeating with same args has no additional effect |
| `openWorldHint` | `true` | Tool interacts with unpredictable external entities |

**Practical defaults:** If no annotations are provided, assume worst case: non-read-only, potentially destructive, non-idempotent, open world. This is deliberately cautious.

**Use cases:**
- `readOnlyHint: true` → client may auto-approve without confirmation
- `destructiveHint: true` → client shows confirmation dialog (e.g., file deletion)
- `idempotentHint: true` → safe to retry on network failure
- `openWorldHint: false` → closed domain (internal DB), predictable behavior

### 7.3 ToolExecution and Tasks (Experimental, 2025-11-25)

Long-running tools can declare `taskSupport`:
- `"forbidden"` (default) — does not support task-augmented execution
- `"optional"` — may support tasks
- `"required"` — requires task-augmented execution

When a client calls a tool with task metadata, the server returns immediately with a `CreateTaskResult` (taskId) and the actual result is retrieved later via `tasks/result`.

**Task states:** `working`, `input_required`, `completed`, `failed`, `cancelled`

### 7.4 Resources

Resources are application-controlled data access points — read-only, no significant computation, no side effects. Analogous to GET endpoints.

**Resource definition:**
```json
{
  "uri": "file:///home/user/project/README.md",
  "name": "Project README",
  "description": "Main project documentation",
  "mimeType": "text/markdown",
  "annotations": {
    "audience": ["user", "assistant"],
    "priority": 0.8,
    "lastModified": "2025-06-01T10:00:00Z"
  }
}
```

**Resource template (URI template pattern):**
```json
{
  "uriTemplate": "github://repos/{owner}/{repo}/issues/{number}",
  "name": "GitHub Issue",
  "description": "Access any GitHub issue by owner, repo, and number",
  "mimeType": "application/json"
}
```

**Reading a resource:**
```json
// Request
{ "method": "resources/read", "params": { "uri": "file:///path/to/file.txt" } }

// Response
{
  "contents": [{
    "uri": "file:///path/to/file.txt",
    "mimeType": "text/plain",
    "text": "File contents here..."
  }]
}
```

Binary content uses `blob` (base64) instead of `text`.

**Resource subscriptions:** If server declares `resources.subscribe: true`, clients can call `resources/subscribe` with a URI. Server sends `notifications/resources/updated` when that resource changes.

**ResourceLink:** Tool results can return `resource_link` content blocks pointing to large datasets without embedding them inline. The `ResourceLink` type carries `uri`, `name`, `mimeType`, `description`, and optional annotations.

### 7.5 Prompts

Prompts are user-controlled reusable templates that standardize common LLM interaction patterns.

**Prompt definition:**
```json
{
  "name": "code_review",
  "title": "Code Review",
  "description": "Performs a thorough code review of the provided code",
  "arguments": [
    {
      "name": "language",
      "title": "Programming Language",
      "description": "The programming language (e.g., 'python', 'typescript')",
      "required": true
    },
    {
      "name": "focus",
      "description": "Specific concerns to focus on (e.g., 'security', 'performance')",
      "required": false
    }
  ]
}
```

**Getting a prompt:**
```json
// Request
{
  "method": "prompts/get",
  "params": {
    "name": "code_review",
    "arguments": { "language": "python", "focus": "security" }
  }
}

// Response
{
  "description": "Security-focused Python code review",
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Please review this Python code focusing on security vulnerabilities..."
      }
    }
  ]
}
```

In Claude Code, MCP prompts appear as slash commands: `/mcp__servername__promptname [args]`

### 7.6 Sampling

Sampling allows MCP **servers** to request LLM completions through the **client**. The server doesn't need its own API key — it leverages the client's existing LLM access.

**Server sends (sampling/createMessage request):**
```json
{
  "method": "sampling/createMessage",
  "params": {
    "messages": [
      {
        "role": "user",
        "content": { "type": "text", "text": "Summarize this data: ..." }
      }
    ],
    "maxTokens": 500,
    "modelPreferences": {
      "hints": [{ "name": "claude-3-5-sonnet" }],
      "costPriority": 0.3,
      "speedPriority": 0.5,
      "intelligencePriority": 0.8
    },
    "systemPrompt": "You are a data analysis assistant."
  }
}
```

**Key design:** Client has **full discretion** over which model to use and **MUST inform the user** before sampling (human-in-the-loop). The spec explicitly states clients should let users inspect and approve sampling requests.

**New in 2025-11-25:** Sampling with tools (`ClientCapabilities.sampling.tools`) — servers can run agentic loops using the client's tokens with full tool-calling support.

### 7.7 Roots

Roots restrict the filesystem scope a server can access. Clients provide a list of `Root` objects (file URIs) that define the boundaries of the server's operational domain.

```json
// Client responds to roots/list request
{
  "roots": [
    {
      "uri": "file:///home/user/projects/my-project",
      "name": "My Project"
    }
  ]
}
```

Servers use roots to understand their operational context. Roots must use `file://` URIs (other URI schemes may be added in future).

In Claude Code: The `CLAUDE_PROJECT_DIR` environment variable is passed to stdio servers. Servers can also call `roots/list` to get the project root.

### 7.8 Elicitation

Servers can request structured user input mid-task through the client.

**Form mode** (non-sensitive data):
```json
{
  "method": "elicitation/request",
  "params": {
    "mode": "form",
    "message": "Please provide your database connection details:",
    "requestedSchema": {
      "type": "object",
      "properties": {
        "host": { "type": "string", "description": "Database host" },
        "port": { "type": "integer", "description": "Port number" }
      },
      "required": ["host"]
    }
  }
}
```

**URL mode** (OAuth, credentials — introduced 2025-11-25):
Sends users to an external URL for credential acquisition. Credentials never transit through the MCP client. The client MUST show the URL to the user before opening it, and MUST NOT pre-fetch the URL.

**FastMCP usage:**
```python
result = await ctx.elicit(
    message="Confirm action?",
    schema=ConfirmationSchema
)
if result.action == "accept":
    # proceed
```

### 7.9 Completions

Servers declaring `completions` capability can provide argument autocomplete suggestions:

```json
// Request
{
  "method": "completion/complete",
  "params": {
    "ref": { "type": "ref/prompt", "name": "code_review" },
    "argument": { "name": "language", "value": "py" }
  }
}

// Response
{
  "completion": {
    "values": ["python", "pydantic", "pytest"],
    "total": 3,
    "hasMore": false
  }
}
```

### 7.10 Logging

Servers can send log messages to clients:
```json
{
  "method": "notifications/message",
  "params": {
    "level": "info",
    "logger": "my-server",
    "data": "Processing request for user 42"
  }
}
```

Log levels: `debug`, `info`, `notice`, `warning`, `error`, `critical`, `alert`, `emergency`

Clients can set the minimum log level with `logging/setLevel`.

### 7.11 Progress Notifications

For long-running tool calls, servers send progress updates using the token from the original request:
```json
{
  "method": "notifications/progress",
  "params": {
    "progressToken": "token-from-request",
    "progress": 0.65,
    "total": 1.0,
    "message": "Processing file 13/20"
  }
}
```

### 7.12 Pagination

For `tools/list`, `resources/list`, `prompts/list`, and `resources/templates/list`:

```json
// Request (first page)
{ "method": "tools/list", "params": {} }

// Response
{
  "tools": [...],
  "nextCursor": "eyJvZmZzZXQiOiAyMH0="
}

// Request (next page)
{ "method": "tools/list", "params": { "cursor": "eyJvZmZzZXQiOiAyMH0=" } }
```

Cursors are opaque strings — clients must not interpret them. Three cursor design strategies:
1. **Index-based**: base64-encoded offset (simple but unstable if items added)
2. **ID-based**: last-seen item ID (stable)
3. **Encoded state**: base64 JSON with multiple fields (for complex sorting/filtering)

**Two separate pagination concerns:**
1. **Catalog pagination** (`tools/list` etc.) — built into spec
2. **Result pagination** (paginating data returned by tool calls) — not standardized; implement with `limit`/`offset` or `cursor` parameters in tool inputSchema

### 7.13 Server Instructions Field

The `instructions` field in `InitializeResult` is a plain string injected into the system prompt by supporting clients (Claude Code, VS Code Copilot, Goose).

**What to include:**
- Workflow guidance (e.g., "call `search_code` before `read_file`")
- Parameter conventions unique to this server
- Cross-cutting constraints that no single tool description can carry
- When NOT to use specific tools

**Critical principles:**
- If a client ignores instructions, each tool must still be usable from its own description alone
- "No instructions are better than poorly written instructions" — write carefully
- Claude Code truncates tool descriptions and server instructions at 2KB each; put critical details first

---

## 8. Building MCP Servers

### 8.1 Python SDK (FastMCP)

FastMCP is the high-level Python interface (part of the official `mcp` package). It handles protocol compliance, connection management, and message routing.

**Installation:**
```bash
uv add "mcp[cli]"
# or
pip install "mcp[cli]"
```

**Minimal server:**
```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers together."""
    return a + b

if __name__ == "__main__":
    mcp.run()  # stdio by default
```

**Full tool with annotations and Pydantic:**
```python
from mcp.server.fastmcp import FastMCP, Context
from pydantic import BaseModel, Field, ConfigDict
from typing import Optional
import httpx

mcp = FastMCP("github_mcp")

class SearchInput(BaseModel):
    model_config = ConfigDict(str_strip_whitespace=True, extra='forbid')
    
    query: str = Field(..., description="Search query (e.g., 'python async best practices')", min_length=2)
    limit: Optional[int] = Field(default=20, description="Max results (1-100)", ge=1, le=100)

@mcp.tool(
    name="github_search_code",
    annotations={
        "title": "Search GitHub Code",
        "readOnlyHint": True,
        "destructiveHint": False,
        "idempotentHint": True,
        "openWorldHint": True
    }
)
async def github_search_code(params: SearchInput, ctx: Context) -> str:
    """Search GitHub code repositories.
    
    Use when: finding code examples, locating implementations.
    Do not use when: you need to create or modify files.
    
    Returns: JSON with items array, each having name, path, url, repository.
    """
    await ctx.report_progress(0.1, "Searching GitHub...")
    try:
        async with httpx.AsyncClient() as client:
            r = await client.get(
                "https://api.github.com/search/code",
                params={"q": params.query, "per_page": params.limit},
                headers={"Accept": "application/vnd.github.v3+json"},
                timeout=30
            )
            r.raise_for_status()
        await ctx.report_progress(1.0, "Done")
        return r.text
    except httpx.HTTPStatusError as e:
        if e.response.status_code == 429:
            return "Error: Rate limit exceeded. Wait before retrying."
        return f"Error: GitHub API returned {e.response.status_code}"
    except httpx.TimeoutException:
        return "Error: Request timed out. Try again."
```

**Resources:**
```python
@mcp.resource("config://settings/{key}")
async def get_setting(key: str) -> str:
    """Expose configuration as a resource."""
    settings = {"theme": "dark", "language": "en"}
    return settings.get(key, f"Key '{key}' not found")
```

**Prompts:**
```python
@mcp.prompt()
def code_review_prompt(language: str, code: str) -> str:
    """Generate a code review prompt."""
    return f"Review this {language} code for bugs and improvements:\n\n```{language}\n{code}\n```"
```

**Lifespan management:**
```python
from contextlib import asynccontextmanager
from dataclasses import dataclass

@dataclass
class AppContext:
    db: Database

@asynccontextmanager
async def app_lifespan(server: FastMCP):
    db = await Database.connect(os.environ["DB_URL"])
    try:
        yield AppContext(db=db)
    finally:
        await db.disconnect()

mcp = FastMCP("my-server", lifespan=app_lifespan)

@mcp.tool()
async def query_db(sql: str, ctx: Context) -> str:
    db = ctx.request_context.lifespan_context.db
    results = await db.execute(sql)
    return str(results)
```

**Context object capabilities:**
- `ctx.report_progress(progress, total, message)` — progress updates
- `await ctx.info(msg)` / `ctx.debug(msg)` / `ctx.error(msg)` — logging
- `await ctx.read_resource(uri)` — read another resource
- `await ctx.elicit(message, schema)` — request user input
- `ctx.request_id` — unique request identifier
- `ctx.client_id` — client identifier

**Transport options:**
```python
mcp.run()                                    # stdio (default)
mcp.run(transport="streamable-http")         # HTTP on :8000/mcp
mcp.run(transport="streamable-http", port=3000, host="0.0.0.0")
```

**Dev/test:**
```bash
uv run mcp dev server.py        # Launch MCP Inspector for testing
uv run mcp install server.py    # Register with Claude Desktop
```

**Sending notifications from FastMCP:**
```python
await mcp.send_resource_list_changed()
await mcp.send_tool_list_changed()
await mcp.send_resource_updated("file:///path/to/file")
```

### 8.2 TypeScript SDK

**Installation:**
```bash
npm install @modelcontextprotocol/sdk zod
```

**Server with tool:**
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-server",
  version: "1.0.0"
});

server.registerTool(
  "github_search_code",
  {
    title: "Search GitHub Code",
    description: `Search GitHub code repositories.
    
Use when: finding code examples, locating implementations.
Returns: JSON with items array, each having name, path, url, repository.`,
    inputSchema: z.object({
      query: z.string().min(2).describe("Search query"),
      limit: z.number().int().min(1).max(100).default(20)
        .describe("Max results")
    }).strict(),
    annotations: {
      readOnlyHint: true,
      destructiveHint: false,
      idempotentHint: true,
      openWorldHint: true
    }
  },
  async ({ query, limit }) => {
    try {
      const resp = await fetch(
        `https://api.github.com/search/code?q=${encodeURIComponent(query)}&per_page=${limit}`,
        { headers: { Accept: "application/vnd.github.v3+json" } }
      );
      if (!resp.ok) {
        return { content: [{ type: "text", text: `Error: API returned ${resp.status}` }], isError: true };
      }
      const data = await resp.json();
      return {
        content: [{ type: "text", text: JSON.stringify(data, null, 2) }],
        structuredContent: data
      };
    } catch (e) {
      return { content: [{ type: "text", text: `Error: ${e instanceof Error ? e.message : String(e)}` }], isError: true };
    }
  }
);

// stdio transport
const transport = new StdioServerTransport();
await server.connect(transport);
```

**Streamable HTTP transport:**
```typescript
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import express from "express";

const app = express();
app.use(express.json());

app.post("/mcp", async (req, res) => {
  // New transport per request for stateless operation
  const transport = new StreamableHTTPServerTransport({
    sessionIdGenerator: undefined,  // stateless
    enableJsonResponse: true
  });
  res.on("close", () => transport.close());
  await server.connect(transport);
  await transport.handleRequest(req, res, req.body);
});

app.listen(3000, () => console.error("MCP server on :3000/mcp"));
```

**IMPORTANT API note:** Use `server.registerTool()`, `server.registerResource()`, `server.registerPrompt()`. Do NOT use the old `server.tool()` or `server.setRequestHandler()` patterns — they are deprecated.

**Resources in TypeScript:**
```typescript
server.registerResource(
  {
    uri: "config://settings/theme",
    name: "Theme Setting",
    mimeType: "application/json"
  },
  async (uri) => ({
    contents: [{ uri, mimeType: "application/json", text: '{"value":"dark"}' }]
  })
);
```

**Prompts in TypeScript:**
```typescript
server.registerPrompt(
  "code_review",
  {
    title: "Code Review",
    description: "Review code for bugs and improvements",
    arguments: [
      { name: "language", description: "Programming language", required: true }
    ]
  },
  async ({ language }) => ({
    messages: [{
      role: "user",
      content: { type: "text", text: `Review this ${language} code:` }
    }]
  })
);
```

**Programmatic client (TypeScript):**
```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";
import { StreamableHTTPClientTransport } from "@modelcontextprotocol/sdk/client/streamableHttp.js";

// stdio client
const transport = new StdioClientTransport({
  command: "node",
  args: ["dist/server.js"]
});
const client = new Client({ name: "my-client", version: "1.0.0" }, { capabilities: {} });
await client.connect(transport);

const tools = await client.listTools();
const result = await client.callTool("tool_name", { param: "value" });

// HTTP client
const httpTransport = new StreamableHTTPClientTransport(new URL("http://localhost:3000/mcp"));
await client.connect(httpTransport);
```

**Python programmatic client:**
```python
from mcp import ClientSession
from mcp.client.stdio import stdio_client, StdioServerParameters

params = StdioServerParameters(command="python", args=["server.py"])
async with stdio_client(params) as (read, write):
    async with ClientSession(read, write) as session:
        await session.initialize()
        tools = await session.list_tools()
        result = await session.call_tool("tool_name", {"arg": "value"})
```

### 8.3 Other Official SDKs

- **Kotlin** — `io.modelcontextprotocol:kotlin-sdk`
- **Java** — `io.modelcontextprotocol.sdk:mcp`
- **C#** — `ModelContextProtocol` NuGet package (supports tasks natively)
- **Rust** — `rust-mcp-schema`, `rmcp` crates

---

## 9. Client Integration Reference

### 9.1 Claude Desktop

Config file locations:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    },
    "remote-api": {
      "url": "https://mcp.example.com/mcp",
      "headers": { "Authorization": "Bearer your-token" }
    }
  }
}
```

After editing, completely quit and restart Claude Desktop.

### 9.2 Claude Code (CLI)

**Adding servers:**
```bash
# HTTP server
claude mcp add --transport http notion https://mcp.notion.com/mcp

# HTTP with auth header
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_PAT"

# stdio server
claude mcp add --env AIRTABLE_API_KEY=YOUR_KEY --transport stdio airtable \
  -- npx -y airtable-mcp-server

# SSE (deprecated)
claude mcp add --transport sse asana https://mcp.asana.com/sse

# WebSocket (via JSON config)
claude mcp add-json events-server \
  '{"type":"ws","url":"wss://mcp.example.com/socket","headers":{"Authorization":"Bearer TOKEN"}}'

# From JSON config
claude mcp add-json weather '{"type":"http","url":"https://api.weather.com/mcp"}'
```

**Scopes:**
```bash
--scope local    # Default. Current project only, stored in ~/.claude.json
--scope project  # Shared via .mcp.json (committed to version control)
--scope user     # All projects, stored in ~/.claude.json
```

**Management:**
```bash
claude mcp list                    # List all configured servers
claude mcp get <name>              # Details for specific server
claude mcp remove <name>           # Remove a server
claude mcp add-from-claude-desktop # Import from Claude Desktop (macOS/WSL)
claude mcp reset-project-choices   # Reset .mcp.json approval decisions
claude mcp serve                   # Run Claude Code itself as an MCP server
```

**Environment variables:**
- `MCP_TIMEOUT=10000` — startup timeout in ms
- `MAX_MCP_OUTPUT_TOKENS=50000` — max output token limit (default 25k)
- `ENABLE_TOOL_SEARCH=true|false|auto|auto:N` — tool search behavior
- `CLAUDE_PROJECT_DIR` — set by Claude Code in server's env (for stdio servers)

**Project `.mcp.json` format:**
```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      },
      "alwaysLoad": false,
      "timeout": 120000
    },
    "local-tool": {
      "command": "${CLAUDE_PROJECT_DIR}/scripts/mcp-server",
      "args": ["--config", "config.json"],
      "env": { "LOG_LEVEL": "info" }
    }
  }
}
```

**`alwaysLoad: true`** — exempts a server from deferred tool loading. Use sparingly for tools needed on every turn.

**Tool search (Claude Code-specific feature):**
By default, MCP tool schemas are deferred and loaded on demand. Claude uses a `ToolSearch` call to discover relevant tools. Only tools actually used enter context. Requires Sonnet 4+ or Opus 4+.

**`_meta.anthropic/maxResultSizeChars`** — in `tools/list` response, lets a tool declare it can return up to N chars (max 500k) bypassing the default threshold.

**`_meta.anthropic/alwaysLoad: true`** — in `tools/list` response, marks a specific tool as always-loaded.

**OAuth flow in Claude Code:**
```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
# Then in Claude Code session:
# /mcp  → follow browser auth
```

**Dynamic headers (for custom auth):**
```json
{
  "type": "http",
  "url": "https://mcp.internal.example.com",
  "headersHelper": "/opt/bin/get-auth-headers.sh"
}
```
The script must output a JSON object of `{"Header-Name": "value"}` to stdout. Runs with 10-second timeout on each connection.

**MCP prompts as slash commands:**
In Claude Code, MCP prompts appear as `/mcp__servername__promptname [args]`

**MCP resources as @ mentions:**
`@github:issue://123` or `@docs:file://api/authentication`

### 9.3 Cursor IDE

Config files:
- Project-scoped: `.cursor/mcp.json`
- Global: `~/.cursor/mcp.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    }
  }
}
```

Access via Settings > Tools & MCP. Resources supported since Cursor v1.6, elicitation since v1.5.

### 9.4 Windsurf

Uses the `mcpServers` structure identical to Claude Desktop. Has a built-in MCP Marketplace with one-click installs via the MCPs icon in Cascade panel.

### 9.5 Cline

Configure via VS Code sidebar: Cline panel → MCP Servers → Configure MCP Servers. Uses separate config from VS Code's built-in MCP support.

### 9.6 Continue

Imports configs from `.continue/mcpServers/`. Can directly import JSON from Claude Desktop, Cursor, or Cline.

### 9.7 Multi-Client Config Note

Most tools use the same `mcpServers` structure. Key difference: VS Code Copilot uses `servers` (not `mcpServers`), and Zed uses `context_servers`.

---

## 10. Tool Design Best Practices

### Naming Conventions

```
{service}_{verb}_{resource}
```

Examples: `github_create_issue`, `slack_send_message`, `postgres_query_table`

Rules:
- Max 64 characters
- Start with a letter (a-z, A-Z)
- Only alphanumeric, underscores, hyphens
- Case-sensitive
- Recommended: `snake_case`
- Include service prefix to avoid conflicts with other servers

### Writing Effective Tool Descriptions

A description is the instruction the LLM reads to decide when and how to use the tool. Treat it like documentation for a careful but ignorant reader.

**Structure:**
1. One sentence summary of what it does
2. When to use it (trigger conditions)
3. When NOT to use it (distinguish from similar tools)
4. Parameter guidance (formats, examples, constraints)
5. Return value description with schema
6. Error conditions and how to recover

**Example:**
```
Search GitHub code repositories for code examples and implementations.

Use when: finding code patterns, locating existing implementations, researching APIs.
Do not use when: you need to create/modify files (use github_create_file) or search issues (use github_search_issues).

Parameters:
  - query: Search string. Supports GitHub code search syntax (e.g., 'language:python async def', 'repo:owner/name pattern')
  - limit: Max results 1-100, default 20. Use 5 for quick exploration, 50+ for exhaustive search.

Returns: JSON with 'items' array. Each item has: name (filename), path (full path), url (GitHub URL), repository.name, repository.full_name.

Errors: Returns "Error: Rate limit exceeded" on 429 — wait 60 seconds before retrying.
```

### Input Schema Design

- Use enums for finite value sets (status, priority, format)
- Include format examples in descriptions: `"e.g., 'owner/repo' like 'octocat/hello-world'"`
- Mark `required` fields explicitly
- Add constraints (`minLength`, `maxLength`, `minimum`, `maximum`)
- Use `default` values for optional parameters
- Add `examples` in parameter descriptions, not as JSON Schema `examples` (clients may not display those)

**Python (Pydantic):**
```python
class CreateIssueInput(BaseModel):
    model_config = ConfigDict(extra='forbid', str_strip_whitespace=True)
    owner: str = Field(..., description="Owner username or org (e.g., 'octocat')", min_length=1)
    repo: str = Field(..., description="Repository name (e.g., 'hello-world')", min_length=1)
    title: str = Field(..., description="Issue title", min_length=1, max_length=256)
    body: Optional[str] = Field(None, description="Issue body in Markdown")
    labels: Optional[list[str]] = Field(default_factory=list, description="Label names to apply")
```

**TypeScript (Zod):**
```typescript
z.object({
  owner: z.string().min(1).describe("Owner username or org (e.g., 'octocat')"),
  repo: z.string().min(1).describe("Repository name (e.g., 'hello-world')"),
  title: z.string().min(1).max(256).describe("Issue title"),
  body: z.string().optional().describe("Issue body in Markdown"),
  labels: z.array(z.string()).default([]).describe("Label names to apply")
}).strict()
```

### Error Handling

Always return errors inside the result object with `isError: true`:

```python
# Good — LLM sees the error and can self-correct
return {
    "isError": True,
    "content": [{"type": "text", "text": "Error: File not found at 'src/missing.py'. Check the path with list_files first."}]
}

# Bad — LLM cannot see this, protocol-level error
raise Exception("File not found")
```

**Error messages should:**
- Be specific about what failed
- Suggest the next step to recover
- Reference related tools when applicable
- Include the input that caused the error

### Response Design

- Support both JSON (programmatic) and Markdown (human-readable) formats via a `response_format` parameter
- For JSON: include all available fields
- For Markdown: use headers, bullets, human-readable timestamps, display names with IDs in parentheses
- Always set a character/token limit to prevent overwhelming context
- Use `structuredContent` (with matching `outputSchema`) for machine-processable outputs

### Pagination

```python
# Tool response with pagination metadata
return json.dumps({
    "total": total_count,
    "count": len(items),
    "offset": params.offset,
    "items": items,
    "has_more": total_count > params.offset + len(items),
    "next_offset": params.offset + len(items) if total_count > params.offset + len(items) else None
})
```

---

## 11. Security

### Authentication

**OAuth 2.1 (spec-standard for remote servers):**
- MCP servers act as OAuth 2.1 **resource servers only** — they validate tokens but do not issue them
- A separate authorization server issues tokens
- PKCE is always required (public clients)
- Servers MUST validate token audience — only accept tokens issued specifically for this server
- Never pass through received tokens to upstream APIs (confused deputy vulnerability)
- Implement Protected Resource Metadata (RFC 9728) at `/.well-known/oauth-protected-resource`

**API keys:**
- Store in environment variables, never in code
- Validate on server startup with clear error messages
- Pass via headers: `Authorization: Bearer <key>` or `X-API-Key: <key>`

**New in 2025-11-25:**
- Client ID Metadata Documents replace Dynamic Client Registration (simpler OAuth setup)
- Machine-to-machine auth via OAuth client credentials (SEP-1046)
- Enterprise SSO: single sign-in to MCP client gives access to all authorized servers

### Input Validation

- Use Pydantic (Python) or Zod (TypeScript) for schema validation — never manual validation
- Sanitize file paths to prevent directory traversal attacks
- Use `execFile` not `exec` for system commands (prevents shell injection)
- Validate all URIs and external identifiers
- Check parameter sizes and ranges

### Prompt Injection

MCP servers are a prime target for prompt injection attacks because:
1. Tool descriptions are injected into the LLM context
2. Tool results containing user-generated content may contain instructions
3. Malicious servers can embed directives in tool descriptions or results

**Mitigations:**
- Only install MCP servers from trusted sources
- Run servers in isolated environments (Docker containers)
- Gateway-layer schema validation can block injected content
- Never have tools that can modify other tool definitions at runtime
- Validate all tool result content before returning it

### Trust Model

Per the MCP specification, there SHOULD always be a human in the loop with the ability to deny tool invocations. The spec explicitly requires:
- Clients inform users before sampling
- Users can approve/deny elicitation requests
- Tool annotations are hints from potentially untrusted servers — clients must not make security decisions based solely on them

### Rate Limiting

For production HTTP servers, implement rate limiting at multiple layers:

**Algorithm:** Token bucket is recommended for LLM workloads — handles bursty tool call patterns while maintaining sustainable average throughput.

**Identifying callers:**
- HTTP servers: API keys, Bearer tokens, or session IDs from headers
- stdio servers: one agent per connection — global limit per process

**Rate limit error message:**
```
"Error: Rate limit exceeded (10 calls/minute). You have 0 tokens remaining. 
Wait 47 seconds before retrying, or reduce call frequency."
```

### DNS Rebinding Protection (local HTTP servers)

For Streamable HTTP servers running locally:
- Validate the `Origin` header on all incoming connections
- Bind to `127.0.0.1` not `0.0.0.0`

---

## 12. MCP Ecosystem

### Official Reference Servers (github.com/modelcontextprotocol/servers)

Active reference implementations:
- **Filesystem** — Secure file operations with configurable access controls
- **Fetch** — Web content fetching and conversion for LLM usage
- **Git** — Read, search, and manipulate Git repositories
- **Memory** — Knowledge graph-based persistent memory system
- **Sequential Thinking** — Dynamic problem-solving through thought sequences
- **Time** — Time and timezone conversion
- **Everything** — Comprehensive test/reference server for all primitives

Note: GitHub, GitLab, PostgreSQL, etc. reference servers are archived at `servers-archived` — use community-maintained versions instead.

### Registries and Marketplaces

| Registry | Servers | Notes |
|----------|---------|-------|
| `registry.modelcontextprotocol.io` | ~2,000 | Official Anthropic registry |
| PulseMCP | 15,000+ | Updated daily |
| Smithery | ~7,300 | |
| Composio | 1,000+ toolkits | 20,000+ tools |
| MCP.so | 17,500+ | |
| Glama | Thousands | |
| MCP Atlas (`mcp-atlas.com`) | Many | Searchable |

### Notable Production Servers

GitHub, Slack, Sentry, Notion, Stripe, HubSpot, Airtable, Asana, PayPal, Docker, Cloudflare, Playwright, Figma, Linear, Jira, PostgreSQL/Bytebase

### MCP Inspector (Testing Tool)

```bash
# Launch interactive inspector UI
npx @modelcontextprotocol/inspector node dist/server.js

# CLI mode
npx @modelcontextprotocol/inspector --cli node dist/server.js --method tools/list
npx @modelcontextprotocol/inspector --cli node dist/server.js --method tools/call --tool-name my_tool --tool-arg key=value
```

UI accessible at `http://localhost:6274`. Tests all primitives: tools, resources, prompts, sampling.

---

## 13. Advanced Patterns

### 13.1 MCP Gateways and Proxies

A gateway/proxy sits in front of multiple MCP servers, providing:
- Unified endpoint for all servers
- Centralized authentication
- Rate limiting and observability
- Tool routing

**Notable implementations:**
- `microsoft/mcp-gateway` — Kubernetes-native, session-aware routing
- `IBM/mcp-context-forge` — Unified gateway for MCP + REST + gRPC APIs
- `MicroMCP` — Micro-service pattern: many single-purpose servers behind one gateway
- Cloudflare Workers + Durable Objects — Serverless MCP hosting

**Proxy pattern (one MCP server acting as client to another):**
```python
# FastMCP proxy — forward requests to another server
from mcp.client.streamable_http import streamablehttp_client

mcp = FastMCP("proxy-server")

@mcp.tool()
async def proxied_tool(query: str) -> str:
    async with streamablehttp_client("https://upstream.example.com/mcp") as (r, w, _):
        async with ClientSession(r, w) as session:
            await session.initialize()
            result = await session.call_tool("upstream_tool", {"query": query})
            return result.content[0].text
```

### 13.2 Multi-Agent Systems with MCP

MCP is for **vertical integration** (agents ↔ tools/data), not agent-to-agent communication.

**Router-Worker pattern:**
A supervisor agent connects to multiple MCP servers and delegates tool calls to appropriate workers:
```
Supervisor (host)
├── MCP Client → Code Analysis Server
├── MCP Client → GitHub Server  
└── MCP Client → Documentation Server
```

**Parallel Evaluator pattern:**
Multiple agents query different MCP servers simultaneously and aggregate results.

**Shared MCP server:**
Deploy one HTTP MCP server that multiple autonomous agents connect to concurrently (requires Streamable HTTP, not stdio).

### 13.3 Resource Subscriptions and Real-Time Updates

```python
# Server side — notify subscribers when resource changes
@mcp.resource("github://repos/{owner}/{repo}/issues")
async def list_issues(owner: str, repo: str) -> str:
    return await fetch_issues(owner, repo)

# When issues change, notify subscribed clients:
await mcp.send_resource_updated(f"github://repos/{owner}/{repo}/issues")
```

```typescript
// Client side — subscribe to resource
await client.subscribeResource("github://repos/octocat/hello-world/issues");
// Client now receives notifications/resources/updated for that URI
```

### 13.4 Streaming Large Results with ResourceLink

For tools returning large datasets, use `ResourceLink` instead of embedding data:

```python
@mcp.tool()
async def run_analytics_query(sql: str) -> list:
    """Run analytics query. Returns summary + link to full results."""
    results = await db.execute(sql)
    
    # Store results, return summary + link
    result_id = await store_results(results)
    
    return [
        TextContent(type="text", text=f"Query returned {len(results)} rows. Top 5:\n{format_top(results[:5])}"),
        ResourceLinkContent(
            type="resource_link",
            uri=f"results://{result_id}",
            name="Full Query Results",
            description=f"Complete {len(results)}-row result set",
            mimeType="application/json"
        )
    ]
```

### 13.5 Tasks for Long-Running Operations (Experimental, 2025-11-25)

```python
# Tool that supports task-augmented execution
@mcp.tool(execution={"taskSupport": "optional"})
async def run_migration(database: str, ctx: Context) -> str:
    """Run database migration. Use task=true for long migrations."""
    # If task metadata provided, return task ID immediately
    # Client polls tasks/get for status, then tasks/result for output
    ...
```

### 13.6 Server-to-Server Authentication

When an MCP server needs to call another MCP server as a client:
- Use OAuth client credentials flow (SEP-1046, 2025-11-25 spec)
- Server authenticates as a machine client, not a user
- No browser redirect needed

---

## 14. Specification Versions

| Version | Released | Key Additions |
|---------|----------|---------------|
| 2024-11-05 | Nov 2024 | Initial release. stdio + SSE transports, tools, resources, prompts, sampling, roots |
| 2025-03-26 | Mar 2025 | Streamable HTTP transport, tool annotations (readOnlyHint etc.), completion/complete |
| 2025-06-18 | Jun 2025 | `outputSchema` + `structuredContent`, elicitation (form mode), ResourceLink, improved OAuth 2.1 |
| 2025-11-25 | Nov 2025 | Tasks primitive (experimental), URL mode elicitation, sampling with tools, extensions framework, client ID metadata documents, OAuth client credentials, standardized tool naming |

**Current stable spec:** `2025-11-25`

The spec uses date-based versioning. Client sends latest supported version; server responds with the version it will use. If client cannot support server's version, it must disconnect.

---

## 15. Common Pitfalls and Debugging

### Pitfalls

1. **Logging to stdout in stdio servers** — corrupts the JSON-RPC stream. Always use stderr.
2. **Protocol errors for tool failures** — use `isError: true` in the result so the LLM can recover.
3. **Missing `--` separator for stdio servers in Claude Code** — `claude mcp add --transport stdio name -- command arg1 arg2`
4. **Not restarting the host after config changes** — Claude Desktop and most clients cache configs on startup.
5. **Ignoring cursor opacity** — cursors are opaque strings; never parse or manipulate them on the client side.
6. **Synchronous code in async handlers** — blocks the event loop. Always use async/await.
7. **Sending every tool schema on every request** — this is function calling behavior. MCP loads tool schemas during session initialization.
8. **Missing `__` double-underscore** in the wrong place — Claude Code normalizes MCP prompt names to `/mcp__servername__promptname`.
9. **Using deprecated SSE transport for new servers** — use Streamable HTTP.
10. **Trusting tool annotations as security guarantees** — they are hints only.

### Debugging Checklist

1. Run `mcp dev server.py` (Python) or `npx @modelcontextprotocol/inspector node dist/server.js` to test with MCP Inspector
2. Check stderr output for errors
3. Verify JSON syntax in config files (JSON is strict about trailing commas)
4. For Claude Code: use `/mcp` to check server status and tool count
5. For Cursor: check Settings > Tools & MCP for server status
6. Test each tool individually via Inspector CLI before connecting to a host
7. Check `protocolVersion` compatibility between client and server
8. Verify all environment variables are set (check `claude mcp get <name>`)
9. Ensure stdio servers use `async def` for all tool functions
10. For HTTP servers: confirm the endpoint is `POST /mcp`, not just `/mcp`

---

## 16. Quick Reference Checklists

### New MCP Server Checklist

- [ ] Server name: Python `{service}_mcp`, TypeScript `{service}-mcp-server`
- [ ] Tool names: `{service}_{verb}_{noun}`, snake_case, max 64 chars
- [ ] All tools have description, inputSchema, annotations
- [ ] `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint` set correctly
- [ ] Tool errors use `isError: true` in result, not protocol errors
- [ ] No stdout logging in stdio servers (stderr only)
- [ ] Pagination for list operations with `limit`/`cursor`/`has_more`
- [ ] `outputSchema` + `structuredContent` for structured data tools
- [ ] Error messages are actionable (suggest next steps)
- [ ] Rate limiting and auth for HTTP servers
- [ ] Tested with MCP Inspector

### Adding an MCP Server (User Checklist)

- [ ] Trust the server source before connecting
- [ ] Check what permissions/env vars the server needs
- [ ] Add with correct transport (`--transport http`, `stdio`, `sse`)
- [ ] Set required env vars (`--env KEY=value`)
- [ ] Choose correct scope (`--scope local|project|user`)
- [ ] Restart the host application
- [ ] Verify connection via `/mcp` (Claude Code) or Settings (Cursor/Windsurf)
