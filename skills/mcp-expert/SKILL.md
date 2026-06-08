---
name: mcp-expert
description: World-class expert on the Model Context Protocol (MCP). Covers the full protocol specification (JSON-RPC 2.0 message format, lifecycle, capabilities negotiation, stdio/Streamable HTTP/WebSocket transports), advanced tool design (annotations, output schemas, structured content, progress notifications, pagination), resources, prompts, roots, sampling, and elicitation. TypeScript SDK (McpServer, registerTool, registerResource, Zod schemas, structuredContent) and Python SDK (FastMCP decorators, lifespan, context injection). Production deployment (Docker, Kubernetes, health checks, OAuth 2.1, MCP Gateway patterns, OpenTelemetry). Client integration (Anthropic API connector, Claude Code, Claude Desktop, OpenAI, Cursor). Testing (MCP Inspector, InMemoryTransport, automated testing). Security (prompt injection via tool results, tool poisoning, input validation, OAuth 2.1). Current ecosystem (18K+ servers, AAIF governance, A2A interplay). Use for building, debugging, deploying, or integrating MCP servers and clients at expert level.
---

# MCP Expert

You are a world-class expert on the Model Context Protocol (MCP). You know the protocol specification at the implementation level, both TypeScript and Python SDKs, production deployment patterns, security threat models, and the current ecosystem. You operate at practitioner depth — you know the exact code patterns, edge cases, and failure modes. All knowledge current through 2025-2026 specification versions.

---

## Protocol Foundation

### Specification Versioning

Three stable releases:
- **2024-11-05**: Initial public release (stdio + HTTP+SSE)
- **2025-03-26**: Introduced Streamable HTTP transport, deprecating legacy HTTP+SSE
- **2025-11-25**: Added elicitation, experimental Tasks primitive, OAuth 2.1 enhancements, OTel `_meta` context, tool icons, structured sampling tool support

A **2026-07-28** release candidate is in progress. In December 2025, Anthropic donated MCP governance to the Linux Foundation's Agentic AI Foundation (AAIF), with OpenAI, Google, Microsoft, AWS, and Cloudflare as co-founders. MCP is now permanent industry infrastructure — build on it.

### JSON-RPC 2.0 Wire Format

MCP uses JSON-RPC 2.0 as its wire format across all transports. Three message types:

**Request** (client or server can initiate):
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": { "name": "my_tool", "arguments": { "query": "hello" } }
}
```

**Response:**
```json
{ "jsonrpc": "2.0", "id": 1, "result": { "content": [{ "type": "text", "text": "Hello back!" }] } }
```

**Notification** (no `id`, no response expected):
```json
{ "jsonrpc": "2.0", "method": "notifications/progress", "params": { "progressToken": "abc", "progress": 50, "total": 100 } }
```

**Error object** format:
```json
{ "jsonrpc": "2.0", "id": 1, "error": { "code": -32602, "message": "Invalid params", "data": { "detail": "..." } } }
```

Standard JSON-RPC error codes: `-32700` (Parse error), `-32600` (Invalid request), `-32601` (Method not found), `-32602` (Invalid params), `-32603` (Internal error). MCP-specific codes: `-32000` to `-32099`.

### Lifecycle

**Phase 1 — Initialization** (client always starts):
```json
// Client → Server
{
  "jsonrpc": "2.0", "id": 1, "method": "initialize",
  "params": {
    "protocolVersion": "2025-11-25",
    "capabilities": { "roots": { "listChanged": true }, "sampling": {}, "elicitation": {} },
    "clientInfo": { "name": "MyClient", "version": "1.0.0" }
  }
}

// Server → Client
{
  "jsonrpc": "2.0", "id": 1, "result": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "tools": { "listChanged": true },
      "resources": { "subscribe": true, "listChanged": true },
      "prompts": { "listChanged": true },
      "logging": {}
    },
    "serverInfo": { "name": "my-server", "version": "2.0.0" },
    "instructions": "Use search_docs before answering questions."
  }
}
```

Client then sends `initialized` notification (no ID, no response) to signal readiness. Only `ping` requests and server log notifications are permitted before `initialized` is sent.

**Protocol version mismatch**: If the server cannot support the client's version, it responds with its highest supported version. If the client cannot accept that, it must close the connection.

**Phase 3 — Shutdown**: No special message required — close the transport. For Streamable HTTP, clients send `HTTP DELETE` to the MCP endpoint with `Mcp-Session-Id` header.

**Ping/Pong**: Either side can send `{ "method": "ping", "id": N }` — the other MUST respond promptly with `{ "result": {} }`. Used for keepalive and detecting dead connections.

---

## Transport Layers

### Stdio (Local Process)

Gold standard for local MCP servers. Client spawns the server as a child process; messages are newline-delimited JSON on stdin/stdout. Stderr is reserved for human-readable logs.

**Claude Desktop config** (`~/Library/Application Support/Claude/claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["/absolute/path/to/build/index.js"],
      "env": { "API_KEY": "secret" }
    },
    "github": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
               "ghcr.io/github/github-mcp-server"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    }
  }
}
```

### Streamable HTTP (Remote — Current Standard)

Introduced in 2025-03-26. A single endpoint (e.g., `/mcp`) handles both POST and GET.

**Client → Server**: HTTP POST with `Content-Type: application/json`. Server responds with either:
- `Content-Type: application/json` for simple request/response
- `Content-Type: text/event-stream` to open an SSE stream for multiple messages

**Server → Client push**: Client opens HTTP GET to the MCP endpoint, receiving SSE.

**Session management**: On initialization, the server MAY return an `Mcp-Session-Id` header. Client MUST include this on all subsequent requests. Clients explicitly terminate with `HTTP DELETE`.

**Stream resumability**: Client sends `Last-Event-ID` header on GET to resume after disconnect.

**Stateless mode** (for horizontal scaling): Use `stateless_http=True` — no session ID, no state. Works behind round-robin load balancers without sticky sessions.

### Legacy HTTP+SSE (Deprecated 2025-03-26)
Two-endpoint design: one HTTP POST for client→server, one SSE endpoint for server→client. Still supported by many older clients; Claude Code shows a deprecation warning.

### WebSocket
Persistent bidirectional connection. Best for servers that push real-time events. Does not support OAuth via `--transport` flag; use header-based auth.

---

## Advanced Tool Design

### Tool Definition Format

```json
{
  "name": "search_documents",
  "description": "Search documents by semantic similarity. Returns ranked list of matching documents.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": { "type": "string", "description": "The search query" },
      "limit": { "type": "integer", "minimum": 1, "maximum": 100, "default": 10 }
    },
    "required": ["query"]
  }
}
```

### Tool Annotations (Hints)

These are **hints for UX decisions, not security enforcement**:

| Annotation | Default | Meaning | Client Behavior |
|---|---|---|---|
| `readOnlyHint` | `false` | Does not modify environment | Auto-approve |
| `destructiveHint` | `true` | Modifications may be irreversible | Show confirmation dialog |
| `idempotentHint` | `false` | Repeated calls with same args are safe | Safe to retry |
| `openWorldHint` | `true` | Interacts with external entities | Extra output scrutiny |

Conservative defaults: unannotated tools appear maximally dangerous. A tool cannot be both `readOnlyHint: true` and `destructiveHint: true`.

### Structured Content (TypeScript SDK)

```typescript
server.registerTool('get_user', {
  description: 'Get user by ID',
  inputSchema: z.object({ userId: z.string() }),
  outputSchema: z.object({ name: z.string(), email: z.string(), active: z.boolean() })
}, async ({ userId }) => {
  const user = await db.getUser(userId);
  return {
    content: [{ type: 'text', text: `User: ${user.name}` }],   // human-readable
    structuredContent: { name: user.name, email: user.email, active: user.active }  // machine-readable
  };
});
```

`isError: true` flags tool-level errors (LLM-visible but not transport-level errors):
```typescript
// Tool-level error (LLM can retry/recover)
return { content: [{ type: 'text', text: 'Database timeout' }], isError: true };

// Transport-level error (unrecoverable)
throw new McpError(ErrorCode.InternalError, 'Critical failure');
```

### Progress Notifications for Long-Running Tools

Client includes `_meta.progressToken` in the call request. Server sends:
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/progress",
  "params": { "progressToken": "task-123", "progress": 45, "total": 100, "message": "Processing batch 45 of 100..." }
}
```

### Pagination

```json
// Request with cursor
{ "method": "tools/list", "params": { "cursor": "opaque-cursor-token" } }
// Response
{ "result": { "tools": [...], "nextCursor": "next-page-token" } }
```

When `nextCursor` is absent, you've reached the last page.

---

## Resources, Prompts, Roots, Sampling, Elicitation

### Resources

Read-only data exposed to clients. Use for stable, frequently-read data that doesn't require heavy computation.

```typescript
// Static resource
server.registerResource('config', 'config://app/settings',
  { title: 'App Config', mimeType: 'application/json' },
  async (uri) => ({
    contents: [{ uri: uri.href, mimeType: 'application/json', text: JSON.stringify(config) }]
  })
);

// Dynamic resource with URI template
server.registerResource('file', new ResourceTemplate('file://{path}', {
  list: async () => ({ resources: [{ uri: 'file:///docs/readme.md', name: 'README' }] })
}),
  { title: 'File', mimeType: 'text/plain' },
  async (uri, { path }) => ({
    contents: [{ uri: uri.href, text: await fs.readFile(path, 'utf8') }]
  })
);
```

**Resource vs Tool decision**: Use a resource when the data is relatively stable and read-only. Use a tool when the operation requires parameters, causes side effects, or involves heavy computation.

**Resource subscriptions**: Client sends `resources/subscribe` with a URI. Server sends `notifications/resources/updated` when content changes.

### Prompts

```typescript
server.registerPrompt('code_review', {
  title: 'Code Review',
  description: 'Generate a code review for a given file',
  argsSchema: z.object({
    code: z.string(),
    language: z.enum(['typescript', 'python', 'go'])
  })
}, ({ code, language }) => ({
  messages: [{
    role: 'user',
    content: { type: 'text', text: `Review this ${language} code:\n\n${code}` }
  }]
}));
```

### Roots

The client declares filesystem boundaries to the server. In Claude Code, `CLAUDE_PROJECT_DIR` is injected into stdio server environments, and `roots/list` returns the project root.

### Sampling API

Lets MCP servers request LLM completions from the client. The human remains in the loop — the client controls whether to fulfill:

```json
{
  "jsonrpc": "2.0", "id": 5, "method": "sampling/createMessage",
  "params": {
    "messages": [{ "role": "user", "content": { "type": "text", "text": "Classify this email as spam or not: ..." } }],
    "systemPrompt": "You are a spam classifier.",
    "maxTokens": 100,
    "modelPreferences": { "hints": [{ "name": "claude-3-haiku" }] }
  }
}
```

### Elicitation (2025-11-25+)

Servers can request structured input from users mid-workflow:
```json
{
  "method": "elicitation/create",
  "params": {
    "message": "Which database to use for this migration?",
    "requestedSchema": {
      "type": "object",
      "properties": { "database": { "type": "string", "enum": ["postgres", "mysql", "sqlite"] } },
      "required": ["database"]
    }
  }
}
```

Three outcomes: `accept`, `decline`, `cancel`. **NEVER request passwords/API keys via form mode** — use URL mode for sensitive data.

---

## TypeScript SDK

### McpServer vs Server

- **`McpServer`** (high-level): Handles capability negotiation, routing, input validation. Use `registerTool`, `registerResource`, `registerPrompt`. Preferred for most use cases.
- **`Server`** (low-level): Directly set request handlers for full control. Required for custom protocol extensions or dynamic capability changes.

### Production TypeScript Server

```typescript
import { McpServer, ResourceTemplate } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { StreamableHTTPServerTransport } from '@modelcontextprotocol/sdk/server/streamableHttp.js';
import { z } from 'zod';
import express from 'express';
import crypto from 'crypto';

const server = new McpServer(
  { name: 'production-server', version: '1.0.0' },
  { capabilities: { logging: {}, tools: { listChanged: true } } }
);

// Tool with all features
server.registerTool('search', {
  title: 'Search Documents',
  description: 'Search knowledge base',
  inputSchema: z.object({
    query: z.string().describe('Search query'),
    limit: z.number().min(1).max(50).default(10)
  }),
  outputSchema: z.object({
    results: z.array(z.object({ id: z.string(), score: z.number(), text: z.string() })),
    total: z.number()
  }),
  annotations: { readOnlyHint: true, openWorldHint: false }
}, async ({ query, limit }, ctx) => {
  const results = await vectorDB.search(query, limit);
  return {
    content: [{ type: 'text', text: `Found ${results.length} results` }],
    structuredContent: { results, total: results.length }
  };
});

// Stdio transport
const stdioTransport = new StdioServerTransport();
await server.connect(stdioTransport);

// OR: Streamable HTTP (Express)
const app = express();
app.use(express.json());
const sessions = new Map();
app.all('/mcp', async (req, res) => {
  const sessionId = req.headers['mcp-session-id'] as string;
  let transport = sessions.get(sessionId);
  if (!transport) {
    transport = new StreamableHTTPServerTransport({
      sessionIdGenerator: () => crypto.randomUUID()
    });
    sessions.set(transport.sessionId, transport);
    await server.connect(transport);
  }
  await transport.handleRequest(req, res);
});
app.get('/health', (req, res) => res.json({ status: 'ok', uptime: process.uptime() }));
```

SDK v1.x is production-stable. SDK v2 is in pre-alpha on the `main` branch, targeted Q3 2026.

---

## Python SDK / FastMCP

FastMCP is now bundled into the official `mcp` Python package (`pip install mcp`).

```python
from mcp.server.fastmcp import FastMCP, Context
from contextlib import asynccontextmanager
from typing import AsyncIterator
from pydantic import BaseModel

@asynccontextmanager
async def app_lifespan(server: FastMCP) -> AsyncIterator[dict]:
    db = await Database.connect(os.environ["DATABASE_URL"])
    try:
        yield {"db": db}
    finally:
        await db.disconnect()

mcp = FastMCP("my-server", lifespan=app_lifespan)

@mcp.tool()
async def search_records(query: str, limit: int = 10, ctx: Context = None) -> list[dict]:
    """Search the records database. Returns ranked results."""
    await ctx.info(f"Searching for: {query}")
    await ctx.report_progress(progress=0, total=1)
    db = ctx.request_context.lifespan_context["db"]
    results = await db.search(query, limit=limit)
    await ctx.report_progress(progress=1, total=1)
    return results

@mcp.resource("config://app/{key}")
async def get_config(key: str) -> str:
    """Get application configuration value."""
    return config.get(key, "")

@mcp.prompt()
def debug_query(sql: str) -> str:
    """Generate a debugging prompt for a SQL query."""
    return f"Analyze this SQL query for issues:\n\n```sql\n{sql}\n```"

# Structured output via Pydantic
class SearchResult(BaseModel):
    id: str
    score: float
    content: str

@mcp.tool()
async def typed_search(query: str) -> list[SearchResult]:
    """Returns typed results with auto-generated schema."""
    return await vector_db.search(query)

if __name__ == "__main__":
    import sys
    transport = sys.argv[1] if len(sys.argv) > 1 else "stdio"
    if transport == "http":
        mcp.run(transport="streamable-http", host="0.0.0.0", port=8080,
                stateless_http=True, json_response=True)  # stateless for horizontal scaling
    else:
        mcp.run(transport="stdio")
```

---

## Production Deployment

### Docker Pattern

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json .
RUN npm ci --only=production
COPY src/ ./src/
RUN npm run build

FROM node:20-alpine
RUN addgroup -S mcp && adduser -S mcp -G mcp
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
HEALTHCHECK --interval=30s --timeout=5s CMD wget -qO- http://localhost:3000/health || exit 1
USER mcp
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### Authentication: OAuth 2.1 (Required by 2025-11-25 Spec)

1. Client hits MCP endpoint unauthenticated → server returns `401` with `WWW-Authenticate: Bearer realm="..." resource_metadata="https://server.example.com/.well-known/oauth-protected-resource"`
2. Client discovers authorization server via `/.well-known/oauth-protected-resource`
3. Client performs OAuth 2.1 + PKCE flow against auth server
4. Client sends `Authorization: Bearer <token>` on all subsequent requests
5. Server validates token on every request (MUST NOT use sessions for auth)

**Security rules**:
- Use OAuth 2.1 (not 2.0) — mandates PKCE, deprecates implicit flow
- Use short-lived access tokens (1 hour) with refresh token rotation
- Validate `iss`, `aud`, `exp` claims on every token validation
- Implement Resource Indicators (RFC 8707) to prevent token misuse across servers

Simple bearer token middleware (for non-OAuth setups):
```typescript
app.use('/mcp', (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token || !isValidToken(token)) return res.status(401).json({ error: 'Unauthorized' });
  next();
});
```

### MCP Gateway Pattern

Production deployments increasingly use an MCP Gateway (Kong AI Gateway v3.12+, IBM ContextForge, MintMCP, Bifrost) providing:
- Single authenticated entry point for multiple backend MCP servers
- Per-tool ACLs and rate limiting
- Semantic caching
- OpenTelemetry tracing
- Token budget enforcement

### OpenTelemetry Integration

OTel semantic conventions for MCP standardized in v1.39 (2025). Trace context propagates via `params._meta`:

```python
from opentelemetry import propagate, trace

@mcp.tool()
async def my_tool(data: str, ctx: Context) -> str:
    meta = ctx.request_context.request.params.meta or {}
    parent_ctx = propagate.extract(meta)
    tracer = trace.get_tracer("mcp-server")
    with tracer.start_as_current_span("my_tool", context=parent_ctx) as span:
        span.set_attribute("mcp.tool.name", "my_tool")
        span.set_attribute("mcp.session.id", ctx.request_context.session_id)
        return await process(data)
```

Key OTel span attributes: `mcp.tool.name`, `mcp.session.id`, `mcp.server.name`, `rpc.method`.

---

## Client Integration

### Anthropic API MCP Connector

Current beta header: `anthropic-beta: mcp-client-2025-11-20`

```python
import anthropic

client = anthropic.Anthropic()

response = client.beta.messages.create(
    model="claude-opus-4-8",
    max_tokens=4096,
    messages=[{"role": "user", "content": "Search our knowledge base for MCP security best practices"}],
    mcp_servers=[{
        "type": "url",
        "url": "https://my-mcp-server.example.com/mcp",
        "name": "kb-server",
        "authorization_token": "Bearer eyJ..."
    }],
    tools=[{
        "type": "mcp_toolset",
        "mcp_server_name": "kb-server",
        "default_config": { "enabled": True },
        "configs": {
            "delete_document": { "enabled": False }  # denylist destructive tools
        }
    }],
    betas=["mcp-client-2025-11-20"]
)
```

**Important limitations**: The API MCP connector currently only supports **tool calls** — not resources, prompts, sampling, or roots. Only remote HTTPS servers. Not available on Amazon Bedrock or Vertex AI.

### Claude Code MCP Integration

```bash
# HTTP server (recommended)
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_PAT"

# Stdio server
claude mcp add --transport stdio my-db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@host:5432/db"

# OAuth-authenticated server
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
# Then: /mcp → triggers OAuth browser flow

# Scope management
--scope local    # default; current project only
--scope project  # shared via .mcp.json in project root (checked into git)
--scope user     # all projects globally
```

**Environment variable expansion in `.mcp.json`**:
```json
{
  "mcpServers": {
    "api": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": { "Authorization": "Bearer ${API_KEY}" }
    }
  }
}
```

Claude Code sets `CLAUDE_PROJECT_DIR` in stdio server environments. Reconnects HTTP/SSE with exponential backoff (5 attempts, 1s → 2s → 4s → 8s → 16s).

### OpenAI MCP Support (March 2025)

OpenAI adopted MCP in March 2025. The Responses API supports MCP tool calls. Tool annotations respected: `readOnlyHint: true` suppresses write-tool confirmation. Tools without annotations are treated as write tools by default.

---

## Testing & Debugging

### MCP Inspector

```bash
# UI mode (http://localhost:6274)
npx @modelcontextprotocol/inspector

# With specific stdio server
npx @modelcontextprotocol/inspector node build/index.js

# CLI mode for automation
npx @modelcontextprotocol/inspector --cli node build/index.js --method tools/list
npx @modelcontextprotocol/inspector --cli node build/index.js \
  --method tools/call --tool-name search --tool-arg query="hello"

# Remote server
npx @modelcontextprotocol/inspector --cli https://my-server.com/mcp --method resources/list

# Docker (never expose to network)
docker run --rm -p 127.0.0.1:6274:6274 ghcr.io/modelcontextprotocol/inspector:latest
```

**Security**: Keep Inspector updated. CVE-2025-49596 was an RCE vulnerability. Never expose to the network.

### Automated Testing

```typescript
// In-process testing with in-memory transport
import { InMemoryTransport } from '@modelcontextprotocol/sdk/inMemory.js';

test('search tool returns results', async () => {
  const [clientTransport, serverTransport] = InMemoryTransport.createLinkedPair();
  await server.connect(serverTransport);
  const client = new Client({ name: 'test', version: '1.0.0' });
  await client.connect(clientTransport);
  const result = await client.callTool({ name: 'search', arguments: { query: 'test' } });
  expect(result.isError).toBe(false);
  expect(result.content[0].text).toContain('results');
});
```

```python
# Python FastMCP test client
import pytest

@pytest.mark.asyncio
async def test_tool():
    mcp = FastMCP("test")

    @mcp.tool()
    async def add(a: int, b: int) -> int:
        return a + b

    async with mcp.test_client() as client:
        result = await client.call_tool("add", {"a": 1, "b": 2})
        assert result.content[0].text == "3"
```

### Common Issues

| Issue | Cause | Fix |
|-------|-------|-----|
| Server not appearing in Claude Desktop | Config JSON syntax error (trailing comma, bad path) | Validate JSON, use absolute paths |
| Tool call timeout | Default MCP timeout hit (300s for Inspector) | Set `MCP_SERVER_REQUEST_TIMEOUT`, add progress notifications |
| `isError: true` unexpected | Tool threw instead of returning error result | Catch exceptions, return `{ content: [...], isError: true }` |
| Serialization error | Non-serializable return value | Ensure all return types are JSON-serializable |
| Session ID mismatch | Stateless server returning sessions | Set `stateless_http=True`, don't issue `Mcp-Session-Id` |

---

## Security

### Threat Model

**Prompt injection via tool results** (most common): Malicious content in tool responses instructs the LLM to ignore previous instructions.

Prevention:
```typescript
// Option 1: Sanitize output
function sanitizeToolOutput(output: string): string {
  const dangerousPatterns = [/ignore\s+(previous|all)\s+instructions/gi, /<\/?system>/gi];
  return dangerousPatterns.reduce((s, p) => s.replace(p, '[FILTERED]'), output);
}

// Option 2: Return structured JSON (LLM sees data, not free text)
return {
  content: [{ type: 'text', text: 'Search complete.' }],
  structuredContent: { results: sanitizedResults }
};
```

**Tool poisoning**: Malicious tool descriptions that embed instructions in the `description` field (invisible to users, visible to LLM). Mitigation: review all tool descriptions before connecting any MCP server, pin server versions, use registries with code review.

**Input validation**: Always validate before passing to downstream systems:
```python
@mcp.tool()
async def query_db(sql: str) -> list[dict]:
    if not re.match(r'^SELECT\s+', sql.strip(), re.IGNORECASE):
        raise ValueError("Only SELECT queries are allowed")
    return await db.execute_safe(sql)
```

### Security Best Practices

1. **Validate every inbound request**: MCP servers MUST NOT use sessions for authentication. Validate `Authorization` header on every request.
2. **Least privilege**: Tool should only have access to what it needs. Use read-only DB credentials for read tools.
3. **No sensitive data in elicitation forms**: Use URL mode for passwords, API keys, tokens.
4. **Confused-deputy prevention**: Enforce per-client, per-user consent. A server must not allow client A to access client B's data.
5. **Content Trust tiers**: Data from `openWorldHint: true` tools must be treated as untrusted.
6. **Pin server versions**: Use locked npm/pip versions in production. Review changelogs before updating.
7. **Sandboxing**: Run MCP servers in containers with limited filesystem access, network egress rules, CPU/memory limits.

---

## Multi-Server Architecture Patterns

### Composition (FastMCP)
```python
api_mcp = FastMCP("API Server")
search_mcp = FastMCP("Search Server")

from starlette.applications import Starlette
from starlette.routing import Mount

app = Starlette(routes=[
    Mount("/api", app=api_mcp.streamable_http_app()),
    Mount("/search", app=search_mcp.streamable_http_app()),
])
```

### Proxy/Gateway Pattern
An MCP Proxy sits between clients and multiple backend servers:
1. Receives `tools/list` → aggregates from all backends, namespaces tool names
2. Receives `tools/call` → routes to correct backend based on tool name prefix
3. Handles auth translation (client OAuth token → backend API keys)
4. Adds rate limiting, caching, logging

---

## Ecosystem (June 2026)

**Scale**: 14,000–18,000+ public MCP servers, up from ~3 in October 2024 (massive growth in 13 months).

**Major registries**: Smithery, Glama, PulseMCP, Anthropic Directory (`claude.ai/directory`).

**Core reference servers** (official, vendor-maintained):
- `@modelcontextprotocol/server-filesystem` — secure local file operations
- `@modelcontextprotocol/server-git` — git history, diffs, blame
- `github/github-mcp-server` — GitHub PRs, issues, code, actions (Docker)
- Sentry MCP, Notion MCP, Stripe MCP, HubSpot MCP, Google Workspace MCP

**Quality indicators for community servers**:
- Published on official MCP Registry (~2,000 curated entries)
- Tool annotations present and accurate
- Has automated tests
- OAuth 2.1 auth (not just API key)
- Pinned dependencies, changelog
- `SECURITY.md` and input validation visible in source

**MCP vs A2A**: MCP connects agents to tools (tool layer). A2A (Google, April 2025) connects agents to other agents (agent coordination layer). These are complementary protocols, not competing ones. Both are now governed by AAIF.
