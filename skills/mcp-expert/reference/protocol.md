# MCP Protocol Deep Dive

## Protocol Foundation

Model Context Protocol (MCP) is built on **JSON-RPC 2.0** over a transport layer. Every interaction — capability negotiation, tool discovery, resource reads, progress reporting, sampling requests — is expressed as a JSON-RPC message. The protocol version is a **date string**, not semver (e.g., `2025-03-26`, `2025-11-25`), enabling calendar-based evolution.

**JSON-RPC 2.0 Message Types:**
- **Requests**: Have an `id`, a `method`, and optional `params`. Expect a response.
- **Responses**: Have the same `id` as the request, plus `result` or `error`.
- **Notifications**: Have a `method` and optional `params` but NO `id`. No response expected.
- **Error object**: `{ code: number, message: string, data?: any }`. Standard codes: `-32700` (parse error), `-32600` (invalid request), `-32601` (method not found), `-32602` (invalid params). Custom MCP code: `-32002` (resource not found) — deprecated in 2026 RC in favor of standard `-32602`.

---

## Core Primitives

### Tools
Tools are **model-controlled functions** — the AI decides when and how to call them. They are the primary action mechanism in MCP.

**Tool Definition Structure:**
```json
{
  "name": "github_create_issue",
  "description": "Creates a new issue in a GitHub repository. Returns the issue URL and number.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "owner": { "type": "string", "description": "Repository owner (username or org)" },
      "repo": { "type": "string", "description": "Repository name" },
      "title": { "type": "string", "description": "Issue title" },
      "body": { "type": "string", "description": "Issue body in markdown" }
    },
    "required": ["owner", "repo", "title"]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "url": { "type": "string" },
      "number": { "type": "integer" }
    }
  },
  "annotations": {
    "readOnlyHint": false,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  }
}
```

**Tool Annotations** (hints, not security guarantees):
- `readOnlyHint`: Tool does not modify state (safe to call without user confirmation)
- `destructiveHint`: Tool may perform irreversible actions (warrants confirmation)
- `idempotentHint`: Repeated calls with same args produce same result
- `openWorldHint`: Tool interacts with external systems beyond the MCP server

**Tool Execution Flow:**
1. Client calls `tools/list` → server returns array of tool definitions
2. Model decides to invoke a tool → client calls `tools/call` with `{ name, arguments }`
3. Server executes and returns `{ content: [...], isError?: boolean }`
4. Content is typed: `{ type: "text", text: string }`, `{ type: "image", data: base64, mimeType }`, or `{ type: "resource", resource: EmbeddedResource }`

**Structured Output (June 2025+):** Servers can now return `structuredContent` (a JSON object matching `outputSchema`) alongside the text content, enabling type-safe processing by clients.

### Resources
Resources are **application-controlled data surfaces** — the host/client decides when to expose them to the model. They are read-only (or minimally mutable) context surfaces identified by URIs.

**Resource Definition:**
```json
{
  "uri": "github://repos/anthropic/claude/issues/123",
  "name": "Issue #123: Fix streaming bug",
  "description": "GitHub issue details",
  "mimeType": "application/json"
}
```

**Static vs Dynamic Resources:**
- **Static**: Fixed URI, fixed content (e.g., a config file, a document)
- **Dynamic (URI Templates)**: Parameterized URIs using RFC 6570 template syntax. Example: `github://repos/{owner}/{repo}/issues/{number}`. Arguments can be auto-completed via the completion API.

**Resource Operations:**
- `resources/list` — enumerate available resources (supports pagination)
- `resources/read` — fetch resource content by URI
- `resources/subscribe` / `resources/unsubscribe` — subscribe to change notifications (optional capability)
- `notifications/resources/updated` — server-to-client notification when a resource changes
- `notifications/resources/list_changed` — server-to-client notification when resource list changes

**URI Scheme Design Principle:** Use domain-specific schemes that reflect the content (`github://`, `db://`, `config://`, `docs://`). The default `mcp://resources/{name}` works for simple cases but domain schemes are more discoverable.

**Resources vs Tools Decision Rule:**
- **Resources** = what the model should *know* (read-only context)
- **Tools** = what the model can *do* (write/action operations)
- If an operation has side effects, it's a tool. If it's purely providing context, it's a resource.

### Prompts
Prompts are **user-controlled templates** — exposed in UI as slash commands, quick actions, or workflow starters. They are reusable, parameterized prompt templates that structure how the AI interacts with the server's domain.

**Prompt Definition:**
```json
{
  "name": "analyze_pr",
  "description": "Performs a thorough code review of a GitHub pull request",
  "arguments": [
    {
      "name": "owner",
      "description": "Repository owner",
      "required": true
    },
    {
      "name": "repo",
      "description": "Repository name",
      "required": true
    },
    {
      "name": "pr_number",
      "description": "Pull request number",
      "required": true
    }
  ]
}
```

**Prompt Operations:**
- `prompts/list` — enumerate available prompts
- `prompts/get` — fetch a prompt with arguments, returns `{ messages: [...] }` ready to inject into conversation

**When to Use Prompts:** Prompts are ideal for complex multi-step workflows with a fixed starting structure, domain-specific interaction patterns (e.g., "review this PR", "explain this database schema"), and user-facing slash commands in Claude Desktop or IDE integrations.

### Sampling (Server-to-Client LLM Requests)
Sampling **flips the flow**: instead of the model calling the server, the server requests an LLM completion FROM the client. This is the mechanism for server-side agentic behavior.

**How It Works:**
1. Server sends `sampling/createMessage` request to client
2. Client shows user the exact prompt and context
3. User may edit, approve, or reject
4. If approved, client forwards to LLM
5. Client shows user the LLM response
6. User may edit, approve, or reject
7. Client sends approved response back to server

**createMessage Request Format:**
```json
{
  "method": "sampling/createMessage",
  "params": {
    "messages": [{ "role": "user", "content": { "type": "text", "text": "..." } }],
    "modelPreferences": {
      "hints": [{ "name": "claude-3-5-sonnet" }],
      "intelligencePriority": 0.8,
      "speedPriority": 0.2
    },
    "systemPrompt": "You are a code reviewer...",
    "maxTokens": 1000
  }
}
```

**Agentic Server Sampling (November 2025, SEP-1577):** Servers can now include tool definitions in sampling requests, enabling server-side agent loops where the server drives multi-step reasoning without requiring the client to maintain agent context.

**CRITICAL Security Note:** There MUST always be a human in the loop with ability to deny sampling requests. Fully automated sampling without human approval is a security anti-pattern that enables prompt injection attacks via tool output.

**Deprecation Notice (2026-07-28 RC):** The `sampling` capability is deprecated in the upcoming 2026 spec in favor of direct LLM API integration. New implementations should avoid depending on it.

### Elicitation (June 2025+)
Elicitation allows servers to **request structured data from users** — essentially server-initiated forms. Example: asking the user to configure credentials, confirm a destructive action, or provide parameters.

**URL Mode Elicitation (November 2025, SEP-1036):** Instead of embedding credential collection in the MCP client, the server sends a URL and the user completes the sensitive flow in a browser. The server receives tokens directly, enabling OAuth flows, PCI-compliant payment processing, and API key management without exposing credentials to the MCP client.

---

## Transport Mechanisms

### stdio Transport
The client launches the MCP server as a **subprocess**. Messages are JSON-RPC objects delimited by newlines over stdin/stdout.

**Technical Rules:**
- Messages sent to **stdin**, received from **stdout**
- Messages MUST NOT contain embedded newlines
- Servers MUST use **stderr** for logging (never stdout, which is reserved for protocol messages)
- The client manages the server process lifecycle

**Best For:**
- Local development tools and IDE integrations (Claude Code, Cursor, VS Code)
- Single-user, single-session scenarios
- Desktop application integrations (Claude Desktop)
- Low-latency local tools where subprocess overhead is acceptable

**Latency Profile:** Typically sub-millisecond for IPC, dominated by tool execution time.

### Streamable HTTP Transport (Current Standard, March 2025+)
The server operates as an **independent HTTP service**. The client connects via HTTP POST for requests and optionally uses Server-Sent Events (SSE) for streaming multiple server messages.

**Technical Specification:**
- Server MUST provide a single endpoint supporting both `POST` and `GET`
- `POST` used for client-to-server requests and notifications
- `GET` used to open SSE stream for server-to-client messages
- Session identity tracked via `Mcp-Session-Id` header (cryptographically secure UUID, JWT, or hash)
- Clients MUST include `Mcp-Session-Id` on all subsequent requests after initialization
- Resume support via `Last-Event-ID` header on reconnect

**Best For:**
- Remote/cloud-hosted MCP servers
- Multi-client scenarios
- Enterprise deployments requiring HTTP infrastructure (load balancers, API gateways)
- Services that need to be consumed by multiple AI clients simultaneously

**Scalability Note (until 2026 RC):** The session-based model requires sticky sessions at the load balancer. This means either sticky session routing (e.g., NGINX `ip_hash`, AWS ALB sticky sessions) or a shared session store (Redis). The 2026-07-28 RC eliminates this requirement entirely by making the protocol stateless.

### HTTP+SSE (DEPRECATED as of March 2025)
The original remote transport used two separate endpoints — one for POST messages, one for the SSE event stream. Deprecated in spec version 2025-03-26. Servers may continue running it for backward compatibility with older clients (pre-March 2025), but new implementations must not use it.

**Why SSE was deprecated:** Required maintaining two endpoints, had connection management complexity, didn't support resumable connections cleanly, and required stateful session management on the server. Streamable HTTP consolidates these into a single endpoint with cleaner semantics.

---

## Session Lifecycle

### Initialization Handshake (Current Spec, 2024-2025)
Three-step process:

**Step 1 — Client sends `initialize`:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "roots": { "listChanged": true },
      "sampling": {}
    },
    "clientInfo": { "name": "claude-desktop", "version": "1.2.0" }
  }
}
```

**Step 2 — Server responds with capabilities:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "tools": { "listChanged": true },
      "resources": { "subscribe": true, "listChanged": true },
      "prompts": { "listChanged": true },
      "logging": {}
    },
    "serverInfo": { "name": "github-mcp", "version": "2.1.0" }
  }
}
```

**Step 3 — Client sends `initialized` notification** (no response expected):
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

### Capability Negotiation
Both client and server commit to respecting only **explicitly negotiated capabilities**. You cannot:
- Invoke a tool on a server that didn't declare `tools` capability
- Request sampling from a client that didn't declare `sampling` capability
- Subscribe to resource changes if server didn't declare `resources.subscribe: true`

The `listChanged` sub-capability signals that the server will send `notifications/tools/list_changed` (or resources, prompts equivalent) when its list of capabilities changes — useful for dynamic servers that add tools at runtime.

### Roots (Deprecated in 2026 RC)
Roots are a client-to-server mechanism declaring which filesystem paths or URIs the client has granted the server access to. Used for scoping resource access in local deployments. **Deprecated in 2026-07-28 RC** — replace by passing scopes as tool parameters or encoding them in resource URIs.

### Stateless Protocol (2026-07-28 RC — Breaking Change)
The upcoming spec eliminates the initialization handshake entirely:
- No `initialize`/`initialized` exchange
- No `Mcp-Session-Id` header
- Client capabilities travel in `_meta` on every request
- New `server/discover` method lets clients fetch server capabilities on demand
- Servers can run behind simple **round-robin load balancers** without sticky sessions
- State becomes application-level, not protocol-level (use explicit handle patterns)

---

## Protocol Versioning & Backward Compatibility

**Version history:**
- `2024-11-05` — Initial public release. HTTP+SSE transport.
- `2025-03-26` — Streamable HTTP transport (replaces SSE). SSE deprecated.
- `2025-06-18` — Structured output, OAuth 2.1, elicitation, security improvements.
- `2025-11-25` — Async Tasks (SEP-1686), simplified OAuth (SEP-991), URL elicitation (SEP-1036), agentic sampling (SEP-1577), enterprise IdP (SEP-990), Extensions framework.
- `2026-07-28 RC` — Stateless protocol, sampling deprecated, roots deprecated, logging deprecated, Tasks as extension, MCP Apps extension, full JSON Schema 2020-12.

**Deprecation Policy (formalized 2026 RC, SEP-2596):** Minimum 12-month overlap between deprecation announcement and removal. Features deprecated in 2026-07-28 RC will not be removed before July 2027.

**Backward Compatibility Strategy:**
- Servers should accept the latest spec version but handle older clients gracefully
- During `initialize`, if client sends older version, server can respond with the version it will use
- For HTTP transport: maintain the deprecated SSE endpoint alongside Streamable HTTP for transition periods
- Use capability negotiation to degrade gracefully (if client doesn't support structured output, fall back to text-only responses)

---

## Task-Based Async Execution (November 2025, SEP-1686)

For long-running operations (data processing, code migration, analytics queries), the synchronous request-response model is insufficient. Tasks provide async execution:

**Flow:**
1. Client calls tool with `{ ..., _meta: { taskHint: true } }`
2. Server immediately returns `{ taskId: "task_abc123", status: "working" }`
3. Client polls with `tasks/get { taskId }` to check status
4. Status transitions: `working` → `input_required` | `completed` | `failed` | `cancelled`
5. When `completed`, client retrieves result via `tasks/get`
6. Client can cancel with `tasks/cancel { taskId }`

**When to Design for Async Tasks:**
- Operations taking >2 seconds
- Operations requiring external system calls with unknown latency
- Multi-step workflows that may need user input mid-execution
- Operations where partial results are not useful

**Note:** In the 2026-07-28 RC, Tasks graduates from core spec to an **Extension** with a stateless redesign.
