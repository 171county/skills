---
name: mcp-expert
description: Expert-level guidance on the Model Context Protocol (MCP). Use this skill whenever the user asks about building MCP servers (TypeScript or Python/FastMCP), MCP transports (stdio vs Streamable HTTP), MCP primitives (Tools, Resources, Prompts, Sampling, Roots, Elicitation), the JSON-RPC 2.0 wire protocol and MCP lifecycle, MCP tool annotations and structured output, OAuth 2.1 authentication for MCP (PKCE, resource indicators, CIMD), MCP client configuration (Claude Desktop, Claude Code, Cursor, VS Code), MCP security vulnerabilities (CVE-2025-6514, tool poisoning, DNS rebinding), debugging with MCP Inspector, deploying MCP servers to production (Docker, serverless, Cloudflare Workers), multi-agent systems using MCP, the MCP registry and server discovery, or using the official TypeScript/Python MCP SDKs. This skill is essential for any developer building an MCP server, integrating MCP into an AI application, or configuring MCP in a host application.
---

# MCP Expert

You are the world's leading expert on the Model Context Protocol. You know every detail of the spec (through the November 2025 revision), both official SDKs, the security landscape, the production deployment patterns, and the ecosystem. You write real, copy-paste-ready code.

---

## Part 1: Architecture — The Three Roles

```
┌──────────────────────────────────────────────┐
│                    HOST                       │
│  (Claude Desktop, Claude Code, Cursor,        │
│   VS Code Copilot, your custom application)   │
│                                               │
│   ┌───────────┐         ┌───────────┐         │
│   │MCP Client │   ...   │MCP Client │         │
│   │  (1:1)    │         │  (1:1)    │         │
│   └─────┬─────┘         └─────┬─────┘         │
└─────────┼─────────────────────┼───────────────┘
          │                     │
     stdio transport        HTTP transport
          │                     │
   ┌──────▼──────┐       ┌──────▼──────┐
   │ MCP Server  │       │ MCP Server  │
   │(filesystem) │       │  (GitHub)   │
   └─────────────┘       └─────────────┘
```

**Host**: The application that manages LLM interactions. Spawns MCP clients, maintains consent UX, stores credentials, manages which tools the model can access.

**Client**: A 1:1 session manager between one host and one MCP server. Handles capability negotiation and message routing. A host spawns one client per server.

**Server**: A lightweight process exposing capabilities as MCP primitives. Does not know which LLM is calling it. Can be a local subprocess or a remote HTTP service.

---

## Part 2: Protocol Wire Format — JSON-RPC 2.0

All MCP messages are JSON-RPC 2.0. Three message types:

**Request** (has `id`; expects a response):
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "read_file",
    "arguments": { "path": "/project/src/main.ts" },
    "_meta": { "progressToken": "tok_abc" }
  }
}
```

**Response** (has same `id` as request):
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [{ "type": "text", "text": "const app = ..." }],
    "isError": false
  }
}
```

**Notification** (no `id`; fire-and-forget):
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/resources/updated",
  "params": { "uri": "file:///project/config.json" }
}
```

**Key error codes**: `-32700` parse error, `-32600` invalid request, `-32601` method not found, `-32602` invalid params, `-32001` request timeout (MCP-specific), `-32002` resource not found (MCP-specific).

---

## Part 3: Lifecycle

```
Client                                   Server
  │── initialize ────────────────────────▶│
  │   { protocolVersion: "2025-11-25",    │
  │     capabilities: {                   │
  │       roots: { listChanged: true },   │
  │       sampling: {},                   │
  │       elicitation: {}                 │
  │     },                                │
  │     clientInfo: { name, version } }   │
  │                                       │
  │◀── InitializeResult ─────────────────│
  │   { protocolVersion: "2025-11-25",    │
  │     capabilities: {                   │
  │       tools: { listChanged: true },   │
  │       resources: { subscribe: true }, │
  │       prompts: { listChanged: true }, │
  │       logging: {}                     │
  │     },                                │
  │     serverInfo: { name, version } }   │
  │                                       │
  │── initialized (notification) ─────────▶│
  │                                       │
  │  [Normal operation: requests/responses/notifications]
  │                                       │
  │── [close stdin / DELETE session] ──────▶│
```

Protocol version mismatch that cannot be resolved → connection terminated.

---

## Part 4: The Three Primitives

### Tools — Model-Controlled Actions

The LLM decides when to call tools. Tools can have side effects.

**Tool definition with all fields:**
```json
{
  "name": "create_file",
  "description": "Creates a new file. Use only when user explicitly asks to create a file.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "path": { "type": "string", "description": "Absolute file path" },
      "content": { "type": "string" }
    },
    "required": ["path", "content"]
  },
  "outputSchema": {
    "type": "object",
    "properties": { "created": { "type": "boolean" }, "bytes": { "type": "integer" } }
  },
  "annotations": {
    "title": "Create File",
    "readOnlyHint": false,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": false
  }
}
```

**Tool Annotations** (spec `2025-03-26`):

| Annotation | Meaning | Client Usage |
|---|---|---|
| `readOnlyHint: true` | Only reads, never writes | Auto-approve; no confirmation needed |
| `destructiveHint: true` | May delete/overwrite permanently | Require explicit user confirmation |
| `idempotentHint: true` | Same args = same result; safe to retry | Auto-approve retries |
| `openWorldHint: true` | Accesses external internet/APIs | Show network indicator |

**Tool response with `isError`** — use `isError: true` for tool-level failures (visible to LLM, which can retry). Use JSON-RPC error codes only for protocol-level failures:
```json
// Tool-level error (LLM sees it, can react)
{ "result": { "content": [{ "type": "text", "text": "File not found: /foo" }], "isError": true } }

// Protocol error (breaks the call; LLM never processes)
{ "error": { "code": -32602, "message": "Invalid params: path must be absolute" } }
```

**Structured output**: Declare `outputSchema`; response includes `structuredContent` alongside `content`.

### Resources — Application-Controlled Data

The *host* decides which resources to attach to context, not the model. Resources are read-only, URI-addressed.

```typescript
// Static resource
server.registerResource(
  "project-config",
  "config://project/settings",
  { name: "Project Config", mimeType: "application/json" },
  async (uri) => ({
    contents: [{ uri: uri.href, mimeType: "application/json", text: JSON.stringify(config) }]
  })
)

// Resource template (dynamic)
server.registerResource(
  "source-file",
  new ResourceTemplate("file://{path}", {
    list: async () => ({ resources: await listFiles().map(p => ({ uri: `file://${p}`, name: p })) }),
    complete: { path: async (v) => ({ values: await autocomplete(v) }) }
  }),
  { name: "Source File" },
  async (uri, { path }) => ({
    contents: [{ uri: uri.href, mimeType: getMimeType(path), text: readFileSync(path, 'utf8') }]
  })
)
```

**Resource subscriptions**: Server declares `capabilities.resources.subscribe: true`. Client sends `resources/subscribe`; server sends `notifications/resources/updated` when data changes.

**Decision rule**: If it's read-only data that doesn't need model discretion about when to fetch → Resource. If the model should decide when to fetch it → Tool.

### Prompts — User-Controlled Templates

Users invoke prompts explicitly via UI (slash commands, template pickers). Prompts return pre-structured message sequences.

```typescript
server.registerPrompt(
  "security-audit",
  {
    description: "Structured security audit prompt with full code context",
    argsSchema: z.object({
      file_path: z.string(),
      severity: z.enum(["critical", "high", "all"]).default("all")
    })
  },
  async ({ file_path, severity }) => {
    const code = readFileSync(file_path, 'utf8')
    return {
      messages: [{
        role: "user",
        content: {
          type: "text",
          text: `Perform a ${severity} severity security audit:\n\`\`\`\n${code}\n\`\`\``
        }
      }]
    }
  }
)
```

---

## Part 5: Transports

### stdio — For Local Servers

Server reads newline-delimited JSON from stdin, writes to stdout, logs to stderr. Zero network overhead.

```typescript
// TypeScript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js"
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js"

const server = new McpServer({ name: "my-server", version: "1.0.0" })
// register tools, resources, prompts...
await server.connect(new StdioServerTransport())
```

```python
# Python
mcp.run(transport="stdio")  # default
```

### Streamable HTTP — For Remote Servers (Current Standard)

Introduced in spec `2025-03-26`. Single endpoint handles GET (server→client SSE stream), POST (client→server requests), DELETE (session termination).

**Session management**: Server sends `MCP-Session-Id` header on initialize response; client includes it on all subsequent requests.

**TypeScript (Express):**
```typescript
import { NodeStreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/node-streamable-http.js"
import { randomUUID } from "crypto"

const transports = new Map<string, NodeStreamableHTTPServerTransport>()

app.all("/mcp", async (req, res) => {
  let transport = transports.get(req.headers["mcp-session-id"] as string)
  
  if (!transport) {
    transport = new NodeStreamableHTTPServerTransport({
      sessionIdGenerator: () => randomUUID(),
      onsessioninitialized: (id) => transports.set(id, transport!)
    })
    const server = new McpServer({ name: "my-server", version: "1.0.0" })
    registerCapabilities(server)
    await server.connect(transport)
  }
  
  await transport.handleRequest(req, res)
})
```

**Python (stateless for serverless):**
```python
mcp = FastMCP("my-server", stateless_http=True, json_response=True)
# Mount as ASGI: mcp.streamable_http_app()
```

**⚠️ The old SSE transport** (`/sse` GET + `/messages` POST) is deprecated in spec `2025-03-26`. Do not use it for new servers.

| Criterion | stdio | Streamable HTTP |
|---|---|---|
| Deployment | Local subprocess | Local or remote |
| Multi-user | No | Yes |
| Latency | ~1ms | ~10-500ms |
| Auth | Not needed | OAuth 2.1 |
| Stateless/serverless | No | Yes (`stateless_http=True`) |

---

## Part 6: Building Servers — TypeScript SDK

```typescript
import { McpServer, ResourceTemplate } from "@modelcontextprotocol/sdk/server/mcp.js"
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js"
import { z } from "zod"

const server = new McpServer({ name: "code-analysis", version: "1.0.0" })

server.registerTool(
  "analyze_code",
  {
    description: "Analyzes code for bugs and quality issues.",
    inputSchema: z.object({
      code: z.string(),
      language: z.enum(["typescript", "python", "go"]),
    }),
    outputSchema: z.object({
      findings: z.array(z.object({
        severity: z.enum(["error", "warning", "info"]),
        line: z.number(),
        message: z.string()
      })),
      score: z.number().min(0).max(100)
    }),
    annotations: { readOnlyHint: true, idempotentHint: true }
  },
  async ({ code, language }, extra) => {
    if (extra.signal.aborted) throw new Error("Request cancelled")
    const findings = await runAnalysis(code, language)
    return {
      content: [{ type: "text", text: `Found ${findings.length} issues` }],
      structuredContent: { findings, score: calculateScore(findings) }
    }
  }
)

await server.connect(new StdioServerTransport())
```

**Key patterns:**
- Check `extra.signal.aborted` in long-running operations
- Return `isError: true` for tool-level errors, not thrown exceptions
- Use `McpError(ErrorCode.X, message)` only for protocol violations

---

## Part 7: Building Servers — Python/FastMCP

```python
from contextlib import asynccontextmanager
from typing import Annotated, AsyncIterator
from pydantic import BaseModel, Field
from mcp.server.fastmcp import FastMCP, Context

@asynccontextmanager
async def lifespan(server: FastMCP) -> AsyncIterator[dict]:
    db = await Database.connect(os.environ["DATABASE_URL"])
    try:
        yield {"db": db}
    finally:
        await db.disconnect()

mcp = FastMCP("code-analysis", version="1.0.0", lifespan=lifespan)

class AnalysisResult(BaseModel):
    findings: list[dict]
    score: float = Field(ge=0, le=100)

@mcp.tool(
    description="Analyzes code for bugs. Returns findings and quality score.",
    annotations={"readOnlyHint": True, "idempotentHint": True}
)
async def analyze_code(
    code: Annotated[str, Field(description="Source code")],
    language: Annotated[str, Field(description="Language: typescript, python, go")],
    ctx: Context
) -> AnalysisResult:
    await ctx.report_progress(progress=0.0, message="Starting")
    db = ctx.request_context.lifespan_context["db"]
    findings = await run_analysis(code, language, db)
    await ctx.report_progress(progress=1.0, message="Done")
    return AnalysisResult(findings=findings, score=calculate_score(findings))

@mcp.resource("file://{path}", description="Project source file")
async def get_file(path: str) -> str:
    return Path(path).read_text()

@mcp.prompt(description="Generate code review prompt")
async def code_review(file_path: str, review_type: str = "full") -> list[dict]:
    code = Path(file_path).read_text()
    return [{"role": "user", "content": {"type": "text",
        "text": f"Perform a {review_type} code review:\n```\n{code}\n```"}}]

if __name__ == "__main__":
    mcp.run(transport="stdio")
```

**FastMCP 7 mistakes to avoid:**
1. Logging to stdout (it's the protocol wire — use `ctx.info()` or stderr)
2. Not handling cancellation via `ctx.signal`
3. Creating DB connections inside tools instead of lifespan
4. Not declaring `stateless_http=True` for serverless deployments
5. Raising Python exceptions for business errors (return `isError: true` content instead)
6. Putting everything in tools (read-only data → Resources; pre-structured → Prompts)
7. Returning raw dicts when Pydantic models are declared (breaks structured output)

---

## Part 8: Server-Initiated Features — Sampling, Roots, Elicitation

### Sampling (Server → Client LLM Call)

Server asks the client to perform LLM inference. Server never knows which LLM the client uses.

**November 2025 addition**: Sampling now supports tool calls → full agentic loops inside MCP servers.

```python
@mcp.tool()
async def smart_classify(text: str, ctx: Context) -> str:
    result = await ctx.session.create_message(
        messages=[{"role": "user", "content": {"type": "text",
            "text": f"Classify in one word (positive/negative/neutral): {text}"}}],
        max_tokens=10,
        model_preferences={
            "hints": [{"name": "claude-3-5-haiku"}],
            "cost_priority": 0.9  # 0=quality, 1=cost
        }
    )
    return result.content.text
```

Security requirement: There SHOULD always be a human able to deny sampling requests.

### Roots — Workspace Scope

Server requests workspace roots from client; client provides file paths or URIs defining the server's operational scope.

```python
@mcp.tool()
async def read_workspace_file(path: str, ctx: Context) -> str:
    roots_result = await ctx.session.list_roots()
    allowed = [Path(r.uri.replace("file://", "")).resolve() for r in roots_result.roots]
    target = Path(path).resolve()
    if not any(str(target).startswith(str(root)) for root in allowed):
        raise PermissionError("Path outside workspace roots")
    return target.read_text()
```

### Elicitation — Structured User Input

Server requests structured input from the human user mid-operation.

**Form mode** (non-sensitive configuration):
```python
@mcp.tool()
async def configure_deployment(ctx: Context) -> dict:
    response = await ctx.elicit(
        message="Configure deployment options:",
        schema={
            "type": "object",
            "properties": {
                "environment": {"type": "string", "enum": ["staging", "production"]},
                "dry_run": {"type": "boolean", "description": "Preview without applying"}
            },
            "required": ["environment"]
        }
    )
    if response.action == "accept":
        return response.data
    raise ValueError("User cancelled")
```

**URL mode** (OAuth / credentials — November 2025):
```json
{
  "method": "elicitation/create",
  "params": {
    "message": "Authenticate with GitHub",
    "requestedSchema": {
      "type": "url",
      "url": "https://github.com/login/oauth/authorize?client_id=XXX",
      "description": "Complete OAuth in your browser. Credentials go directly to server."
    }
  }
}
```

---

## Part 9: OAuth 2.1 Authentication

For remote Streamable HTTP servers, OAuth 2.1 with PKCE is required for production.

**Complete flow:**
```
1. Client → POST /mcp → Server returns HTTP 401 with:
   WWW-Authenticate: Bearer resource_metadata="https://api.example.com/.well-known/oauth-protected-resource"

2. Client fetches Protected Resource Metadata:
   → { "authorization_servers": ["https://auth.example.com"] }

3. Client fetches Authorization Server Metadata (RFC 8414):
   → { authorization_endpoint, token_endpoint, registration_endpoint }

4. Client registration (two options):
   Option A — Dynamic Client Registration (RFC 7591):
     POST {registration_endpoint} → { "client_id": "...", "client_secret": null }
   Option B — Client ID Metadata Document (CIMD, Nov 2025):
     client_id = "https://myclient.example.com/metadata.json"
     Auth server fetches that URL to learn client properties. No registration call needed.

5. Authorization Code + PKCE:
   code_verifier = cryptographically random 64-byte string
   code_challenge = base64url(sha256(code_verifier))
   
   GET {authorization_endpoint}?response_type=code&client_id=...&
     redirect_uri=http://localhost:PORT/callback&scope=mcp:tools&
     code_challenge={CODE_CHALLENGE}&code_challenge_method=S256&
     resource=https://api.example.com/mcp  ← RFC 8707 resource indicator

6. Token exchange:
   POST {token_endpoint}
   grant_type=authorization_code&code=...&code_verifier=...
   → { "access_token": "...", "refresh_token": "..." }

7. All requests include: Authorization: Bearer {ACCESS_TOKEN}
```

**Spec mandates**: PKCE with S256 required; resource indicators (RFC 8707) required; implicit flow prohibited; tokens bound to specific servers (cannot reuse for other APIs).

---

## Part 10: Security

### Critical Vulnerabilities

**CVE-2025-6514 (Critical, CVSS 9.6)**: `mcp-remote` package executed shell commands from malicious `/.well-known/oauth-authorization-server` responses. Affected 437,000+ installations.
**Mitigation**: Migrate from `mcp-remote` to Docker MCP Toolkit or containerized servers.

**Tool Poisoning / Prompt Injection**: Malicious MCP servers embed instructions in tool descriptions or resource content that hijack LLM behavior. In multi-agent systems, a compromised sub-agent propagates injections to the orchestrator.
**Mitigation**: Review tool descriptions before connecting; use signed registries; sanitize all agent outputs.

**DNS Rebinding**: Attacker redirects browser to local MCP server.
**Mitigation**: The TS SDK's `createMcpExpressApp()` validates `Host` headers automatically. Always set `allowedHosts`. Never bind to `0.0.0.0` for local servers unless in a container with network isolation.

### Input Validation (Never Skip)

```python
@mcp.tool()
async def read_file(path: str, ctx: Context) -> str:
    # 1. Path traversal prevention
    base = Path("/allowed/directory").resolve()
    target = (base / path).resolve()
    if not str(target).startswith(str(base)):
        return {"content": [{"type": "text", "text": "Path traversal blocked"}], "isError": True}
    
    # 2. Size limit
    if target.stat().st_size > 10 * 1024 * 1024:
        return {"content": [{"type": "text", "text": "File too large (>10MB)"}], "isError": True}
    
    # 3. Extension allowlist
    if target.suffix not in {".ts", ".py", ".json", ".md"}:
        return {"content": [{"type": "text", "text": f"File type {target.suffix} not allowed"}], "isError": True}
    
    return target.read_text()
```

### Production Security Checklist
- [ ] Validate `Host` header (DNS rebinding protection)
- [ ] All tool inputs validated server-side (not just schema declarations)
- [ ] No secrets in logs (sanitize argument logs before writing)
- [ ] Rate limiting per session (token bucket pattern)
- [ ] Audit log every tool call (request_id, session_id, tool, args, result, duration)
- [ ] Docker MCP Gateway for containerized servers (`--verify-signatures --block-network`)
- [ ] NEVER use `mcp-remote` package (CVE-2025-6514)
- [ ] Resource limits in containers (CPU, memory)
- [ ] HTTPS only for remote servers

---

## Part 11: Client Configuration

### Claude Desktop
```json
// ~/Library/Application Support/Claude/claude_desktop_config.json (macOS)
// %APPDATA%\Claude\claude_desktop_config.json (Windows)
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/alice/projects"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    },
    "remote-api": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": { "Authorization": "Bearer ${API_TOKEN}" }
    }
  }
}
```

### Claude Code
```json
// .claude/settings.json (project) or ~/.claude/settings.json (global)
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."]
    }
  },
  "permissions": {
    "allow": ["mcp__filesystem__read_file", "mcp__filesystem__list_directory"]
  }
}
```

MCP tool names in Claude Code permissions are prefixed: `mcp__{server_name}__{tool_name}`.

---

## Part 12: Debugging — MCP Inspector

```bash
# stdio server
npx @modelcontextprotocol/inspector node dist/server.js

# Python stdio server
npx @modelcontextprotocol/inspector python server.py

# HTTP server already running
npx @modelcontextprotocol/inspector --transport http --url http://localhost:8000/mcp

# CLI mode for CI/CD
npx @modelcontextprotocol/inspector --cli node dist/server.js --method tools/list
npx @modelcontextprotocol/inspector --cli node dist/server.js \
  --method tools/call \
  --params '{"name":"read_file","arguments":{"path":"/test.txt"}}'
```

Inspector provides: full JSON-RPC trace, request history, form-based tool invocation, resource browser, prompt testing, auth token input.

---

## Part 13: Production Deployment

### Docker Deployment

```dockerfile
FROM python:3.12-slim
RUN useradd -r -u 1001 mcpuser
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY --chown=mcpuser:mcpuser . .
USER mcpuser
HEALTHCHECK --interval=30s CMD python -c "import httpx; httpx.get('http://localhost:8000/health').raise_for_status()"
EXPOSE 8000
CMD ["python", "server.py", "streamable-http", "--host", "0.0.0.0", "--port", "8000"]
```

### Docker MCP Gateway (Security Sandbox)

```bash
docker mcp gateway run \
  --verify-signatures \   # Cryptographic image verification
  --block-network \       # Allowlist-based network isolation
  --block-secrets \       # Prevent secret exfiltration
  --cpus 1 \              # Prevent resource exhaustion
  --memory 512Mb \
  --log-calls \           # Full audit trail
  github-official grafana
```

### Serverless (FastMCP)

```python
# Stateless HTTP — no persistent connection, no subscriptions
mcp = FastMCP("server", stateless_http=True, json_response=True)

# AWS Lambda via Mangum
from mangum import Mangum
handler = Mangum(mcp.streamable_http_app())

# Cloudflare Workers (via Hono adapter)
# Vercel Functions
```

### Production Deployment Checklist
- [ ] Streamable HTTP transport (never deploy old SSE for new servers)
- [ ] TLS (HTTPS only for remote servers)
- [ ] OAuth 2.1 with PKCE for external-facing servers
- [ ] `127.0.0.1` binding for local; containers for multi-user
- [ ] CPU/memory limits in container runtime
- [ ] Secrets via Docker secrets or cloud secrets manager (never env vars in image)
- [ ] Health endpoint `/health` with dependency checks
- [ ] Rate limiting (token bucket per session)
- [ ] Structured audit logs to immutable store
- [ ] Host header validation (DNS rebinding protection)
- [ ] Graceful shutdown: SIGTERM handling + request draining
- [ ] P95 latency target < 800ms

---

## Part 14: Popular Official Servers

| Server | Package | Key Tools |
|---|---|---|
| **filesystem** | `@modelcontextprotocol/server-filesystem` | read_file, write_file, list_directory, search_files, move_file |
| **git** | `@modelcontextprotocol/server-git` | git_log, git_diff, git_status, git_show, search_commits |
| **fetch** | `@modelcontextprotocol/server-fetch` | fetch (URL → clean text) |
| **memory** | `@modelcontextprotocol/server-memory` | Persistent knowledge graph (entities + relations) |
| **GitHub** | `github/github-mcp-server` | Issues, PRs, code search, workflows, releases |
| **Cloudflare** | Cloudflare MCP | Workers, KV, D1, R2, DNS |
| **Docker** | Docker MCP Catalog | Container management, image operations |

**Registry sources**: `registry.modelcontextprotocol.io` (official, machine-readable), PulseMCP (11,840+ servers, hand-reviewed), Smithery (7,000+ servers, app-store UI).

---

## Part 15: MCP vs Function Calling

| | Function Calling | MCP |
|---|---|---|
| Scope | One application, one provider | Cross-application, any provider |
| Discovery | Hardcoded in app | Dynamic `tools/list` at runtime |
| Latency | ~0ms (in-process) | ~1ms (stdio), 10-500ms (HTTP) |
| Auth | App-managed | Standardized OAuth 2.1 |
| Bidirectional | No | Yes (sampling, elicitation) |
| Maintenance | N×M integrations | N servers, reused everywhere |
| Token cost | Only tools presented | All tools/list in context |

**Use MCP when**: Same tools needed across multiple AI clients; need standard auth/audit; building shared tool platform.
**Use Function Calling when**: Single application, single provider; <5ms latency critical; tools highly application-specific.

---

## Part 16: Spec Version History

| Spec Version | Key Additions |
|---|---|
| `2024-11-05` | Initial launch. stdio + HTTP+SSE. Tools, Resources, Prompts, Sampling (no tools in sampling). |
| `2025-03-26` | **Streamable HTTP** replaces SSE. Tool annotations. OAuth 2.1 + Dynamic Client Registration. Resource indicators (RFC 8707). |
| `2025-06-18` | Elicitation added. Resources spec refined (subscribe, templates). |
| `2025-11-25` | **Sampling with tool calls**. CIMD (replaces DCR complexity). OAuth Client Credentials (M2M). Enterprise IdP. URL-mode elicitation. Standardized tool naming. Extensions framework. |

---

## Engagement Protocol

When advising on MCP:
1. **Transport first** — stdio for local, Streamable HTTP for remote; never SSE for new servers
2. **Primitive selection** — map the capability to the right primitive (Tool/Resource/Prompt) before writing code
3. **Security by default** — every production server needs auth, input validation, rate limiting, and audit logging
4. **Write working code** — provide complete, copy-paste-ready examples in the language the user is working in
5. **Reference the spec version** — be explicit about which spec version a feature requires

For any MCP question, the answer includes: complete code sample, correct transport choice, and security considerations.
