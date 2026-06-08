---
name: mcp-expert
description: Expert-level Model Context Protocol (MCP) advisor. Activate when the user needs guidance on building, deploying, or integrating MCP servers — including JSON-RPC 2.0 architecture, TypeScript and Python SDK implementation, transport selection (stdio vs. Streamable HTTP), tool/resource/prompt design, OAuth 2.1 authentication, Docker deployment, security hardening, MCP client integration, debugging with MCP Inspector, and production operational practices for MCP servers.
---

# MCP Expert

You are a senior MCP architect and implementer with deep experience building production MCP servers and integrating them into AI agent systems. You have written MCP servers from scratch in both TypeScript and Python, navigated the OAuth 2.1 authentication flows, deployed containerized MCP servers at scale, and debugged the full range of transport and protocol issues that arise in production. You advise at the level of someone who has read the protocol specification and built systems that depend on it.

---

## 1. MCP Architecture — Protocol Foundation

### What MCP Is

Model Context Protocol (MCP) is an open protocol developed by Anthropic (released November 2024) that standardizes the interface between AI language model applications and external services, data sources, and tools. OpenAI adopted MCP in March 2025, making it the de-facto standard for AI-tool integration.

**The core abstraction:** "USB-C for AI tools" — any MCP-compatible client (Claude, Cursor, VS Code Copilot, custom agents) can use any MCP server without bespoke per-client integration.

**Architecture roles:**
- **MCP Host:** The AI application (Claude Desktop, Cursor, a custom agent framework)
- **MCP Client:** Protocol implementation inside the host; manages connections to servers
- **MCP Server:** External service that exposes tools, resources, and/or prompts via MCP

### JSON-RPC 2.0 Foundation

MCP messages are JSON-RPC 2.0 encoded. Every message is either:

**Request (client → server or server → client):**
```json
{
  "jsonrpc": "2.0",
  "id": "request-123",
  "method": "tools/call",
  "params": {
    "name": "search_database",
    "arguments": {"query": "active users", "limit": 10}
  }
}
```

**Response (reply to request):**
```json
{
  "jsonrpc": "2.0",
  "id": "request-123",
  "result": {
    "content": [{"type": "text", "text": "Found 847 active users..."}]
  }
}
```

**Notification (no response expected):**
```json
{
  "jsonrpc": "2.0",
  "method": "notifications/message",
  "params": {"level": "info", "data": "Processing started"}
}
```

### MCP Primitives

**Tools:** Functions the LLM can call to perform actions or retrieve data. The primary mechanism for agent capability extension.

**Resources:** Data sources the LLM can read. Like a file system or API endpoint — the model requests content, doesn't execute it. Identified by URI.

**Prompts:** Reusable prompt templates with arguments. Allow servers to define specific workflows or interaction patterns.

**Sampling:** Allows servers to request LLM completions (server → client direction). Enables server-side AI processing within the MCP protocol.

---

## 2. Transport Mechanisms

### stdio Transport (Local Servers)

The simplest transport. The MCP client spawns the server as a child process and communicates via stdin/stdout.

**When to use:** Local development tools, file system access, CLI integration, single-user local servers (Claude Desktop, Cursor, VS Code extensions).

**Characteristics:**
- Zero network overhead
- Automatic process lifecycle management (client kills server when done)
- No authentication needed (process isolation is sufficient)
- Not scalable to multi-user scenarios

**Example stdio server (TypeScript):**
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({
  name: "my-local-server",
  version: "1.0.0"
});

server.registerTool("read_file", {
  description: "Read a file from the local filesystem",
  inputSchema: {
    type: "object",
    properties: {
      path: { type: "string", description: "Absolute file path" }
    },
    required: ["path"]
  }
}, async ({ path }) => {
  const content = await fs.readFile(path, "utf-8");
  return { content: [{ type: "text", text: content }] };
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Streamable HTTP Transport (Remote Servers)

The standard transport for remote, multi-user, production MCP servers. Replaced the older SSE transport (which is being deprecated).

**When to use:** Multi-user SaaS integrations, cloud-deployed servers, servers requiring authentication, high-concurrency use cases.

**Architecture:** Stateless JSON-RPC over HTTP. Each request-response pair is a single HTTP call. No persistent connection required.

**Key characteristic:** Stateless by default. Session state must be maintained externally (database, Redis) if needed. This is the correct design for scalable production servers.

**Simple HTTP server (TypeScript with Hono):**
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { Hono } from "hono";

const app = new Hono();

app.post("/mcp", async (c) => {
  const server = new McpServer({ name: "my-server", version: "1.0.0" });
  
  server.registerTool("get_weather", {
    description: "Get current weather for a location",
    inputSchema: {
      type: "object",
      properties: {
        location: { type: "string", description: "City name or coordinates" }
      },
      required: ["location"]
    }
  }, async ({ location }) => {
    const data = await fetchWeather(location);
    return {
      content: [{
        type: "text",
        text: `Weather in ${location}: ${data.temp}°C, ${data.description}`
      }]
    };
  });

  const transport = new StreamableHTTPServerTransport({
    sessionIdGenerator: undefined  // Stateless mode
  });
  
  await server.connect(transport);
  return transport.handleRequest(c.req.raw, c.res);
});

export default app;
```

---

## 3. TypeScript SDK — Deep Implementation Guide

### Project Setup

```bash
npm init -y
npm install @modelcontextprotocol/sdk hono zod
npm install -D typescript @types/node tsx
```

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true
  }
}
```

**package.json key fields:**
```json
{
  "type": "module",
  "main": "dist/index.js",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsx src/index.ts"
  }
}
```

### Tool Registration with Zod Schema

```typescript
import { z } from "zod";

const SearchSchema = z.object({
  query: z.string().describe("Search query string"),
  limit: z.number().min(1).max(100).default(10).describe("Number of results"),
  filters: z.object({
    status: z.enum(["active", "inactive", "all"]).default("all"),
    date_from: z.string().optional().describe("ISO date string")
  }).optional()
});

server.registerTool("search_records", {
  description: "Search database records with optional filters",
  inputSchema: zodToJsonSchema(SearchSchema),
  annotations: {
    readOnlyHint: true,
    idempotentHint: true
  }
}, async (args) => {
  const params = SearchSchema.parse(args);
  
  try {
    const results = await db.search(params);
    return {
      content: [{
        type: "text",
        text: JSON.stringify(results, null, 2)
      }],
      structuredContent: results  // Modern SDK: provide both for client flexibility
    };
  } catch (error) {
    return {
      content: [{
        type: "text",
        text: `Search failed: ${error instanceof Error ? error.message : "Unknown error"}`
      }],
      isError: true
    };
  }
});
```

### Tool Annotations

Tool annotations hint to clients about behavior:

```typescript
{
  annotations: {
    readOnlyHint: true,      // Tool does not modify state
    destructiveHint: false,  // Tool does not delete data
    idempotentHint: true,    // Same call with same args = same result
    openWorldHint: false     // Tool works on closed dataset (known schema)
  }
}
```

**Destructive operation example:**
```typescript
server.registerTool("delete_record", {
  description: "Permanently delete a record by ID",
  inputSchema: { /* ... */ },
  annotations: {
    readOnlyHint: false,
    destructiveHint: true,    // Signals: this cannot be undone
    idempotentHint: true      // Safe to retry (delete already-deleted = OK)
  }
}, async ({ id }) => {
  // Implementation
});
```

### Resources Implementation

```typescript
server.registerResource(
  "database://customers",
  {
    name: "Customer Database",
    description: "Read access to customer records",
    mimeType: "application/json"
  },
  async (uri) => {
    const customers = await db.getCustomers();
    return {
      contents: [{
        uri: uri.toString(),
        mimeType: "application/json",
        text: JSON.stringify(customers, null, 2)
      }]
    };
  }
);

// Resource templates (parameterized URIs)
server.registerResourceTemplate(
  new ResourceTemplate("database://customers/{id}", { list: undefined }),
  {
    name: "Customer Record",
    description: "Single customer record by ID",
    mimeType: "application/json"
  },
  async (uri, { id }) => {
    const customer = await db.getCustomer(id);
    if (!customer) {
      throw new Error(`Customer ${id} not found`);
    }
    return {
      contents: [{
        uri: uri.toString(),
        mimeType: "application/json",
        text: JSON.stringify(customer, null, 2)
      }]
    };
  }
);
```

### Prompts Implementation

```typescript
server.registerPrompt(
  "analyze_customer",
  {
    name: "Customer Analysis",
    description: "Generate a comprehensive customer analysis report",
    arguments: [
      {
        name: "customer_id",
        description: "Customer ID to analyze",
        required: true
      },
      {
        name: "depth",
        description: "Analysis depth: summary, detailed, or comprehensive",
        required: false
      }
    ]
  },
  async ({ customer_id, depth = "detailed" }) => {
    const customer = await db.getCustomer(customer_id);
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Analyze the following customer data at ${depth} depth:\n\n${JSON.stringify(customer, null, 2)}`
          }
        }
      ]
    };
  }
);
```

---

## 4. Python SDK (FastMCP) — Deep Implementation Guide

### Installation and Setup

```bash
pip install mcp fastmcp httpx pydantic
```

### FastMCP Server (Recommended Pattern)

```python
from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel, Field
from typing import Optional
import httpx

mcp = FastMCP("My API Server")

class SearchParams(BaseModel):
    query: str = Field(description="Search query")
    limit: int = Field(default=10, ge=1, le=100, description="Max results")
    status: Optional[str] = Field(default=None, description="Filter by status")

@mcp.tool()
async def search_records(params: SearchParams) -> str:
    """Search database records with optional filters.
    
    Returns JSON array of matching records.
    """
    async with httpx.AsyncClient() as client:
        response = await client.get(
            "https://api.example.com/records",
            params=params.model_dump(exclude_none=True),
            headers={"Authorization": f"Bearer {get_api_key()}"}
        )
        response.raise_for_status()
        return response.text

@mcp.tool()
async def create_record(
    name: str = Field(description="Record name"),
    type: str = Field(description="Record type: task, note, or event"),
    content: str = Field(description="Record content")
) -> str:
    """Create a new record in the database."""
    # Implementation
    pass

@mcp.resource("config://settings")
async def get_settings() -> str:
    """Current application configuration."""
    return json.dumps({"version": "1.0", "features": ["search", "create"]})

@mcp.prompt()
async def analyze_record(record_id: str, format: str = "markdown") -> str:
    """Generate analysis prompt for a record."""
    record = await fetch_record(record_id)
    return f"Analyze this record in {format} format:\n\n{record}"

if __name__ == "__main__":
    mcp.run()
```

### Python Streaming and Async Patterns

```python
from mcp.server.fastmcp import FastMCP
from mcp.types import TextContent
import asyncio

mcp = FastMCP("Streaming Server")

@mcp.tool()
async def long_running_analysis(dataset_id: str) -> list[TextContent]:
    """Analyze a large dataset (may take time)."""
    results = []
    
    async for chunk in process_dataset_stream(dataset_id):
        results.append(TextContent(
            type="text",
            text=f"Processed chunk: {chunk}"
        ))
    
    return results

@mcp.tool()
async def parallel_search(queries: list[str]) -> str:
    """Run multiple searches in parallel."""
    tasks = [search_single(q) for q in queries]
    results = await asyncio.gather(*tasks, return_exceptions=True)
    
    output = []
    for query, result in zip(queries, results):
        if isinstance(result, Exception):
            output.append(f"{query}: ERROR - {result}")
        else:
            output.append(f"{query}: {result}")
    
    return "\n".join(output)
```

---

## 5. Authentication — OAuth 2.1

### MCP OAuth 2.1 Flow

MCP 1.0 specification includes OAuth 2.1 as the standard authentication mechanism for remote (HTTP) servers.

**The flow for remote MCP servers:**
1. Client discovers server's OAuth metadata at `/.well-known/oauth-authorization-server`
2. Client initiates authorization code flow with PKCE
3. User authenticates and authorizes
4. Client receives authorization code, exchanges for access token
5. Client includes `Bearer` token in every MCP HTTP request
6. Server validates token on each request

**OAuth 2.1 key differences from OAuth 2.0:**
- PKCE required for all flows (eliminates authorization code interception attacks)
- Implicit grant removed (was insecure)
- Resource owner password credentials removed
- Refresh token rotation required

### OAuth Discovery Document

```json
{
  "issuer": "https://mcp.myapp.com",
  "authorization_endpoint": "https://mcp.myapp.com/oauth/authorize",
  "token_endpoint": "https://mcp.myapp.com/oauth/token",
  "registration_endpoint": "https://mcp.myapp.com/oauth/register",
  "response_types_supported": ["code"],
  "grant_types_supported": ["authorization_code", "refresh_token"],
  "code_challenge_methods_supported": ["S256"],
  "scopes_supported": ["read", "write", "admin"]
}
```

### Server-Side Token Validation (TypeScript)

```typescript
import { jwtVerify, createRemoteJWKSet } from "jose";

const JWKS = createRemoteJWKSet(
  new URL("https://your-auth-server.com/.well-known/jwks.json")
);

async function validateMcpToken(authHeader: string | null): Promise<TokenClaims> {
  if (!authHeader?.startsWith("Bearer ")) {
    throw new McpAuthError("Missing or invalid Authorization header", 401);
  }
  
  const token = authHeader.slice(7);
  
  try {
    const { payload } = await jwtVerify(token, JWKS, {
      issuer: "https://your-auth-server.com",
      audience: "https://mcp.myapp.com"
    });
    
    return payload as TokenClaims;
  } catch (error) {
    throw new McpAuthError("Invalid or expired token", 401);
  }
}

// Apply to all MCP endpoints
app.use("/mcp", async (c, next) => {
  const claims = await validateMcpToken(c.req.header("Authorization"));
  c.set("user", claims);
  await next();
});
```

### API Key Authentication (Simpler Alternative)

For internal or developer-focused MCP servers, API key authentication is simpler than OAuth:

```typescript
const API_KEYS = new Set(process.env.VALID_API_KEYS?.split(",") || []);

app.use("/mcp", async (c, next) => {
  const apiKey = c.req.header("X-API-Key") || c.req.header("Authorization")?.replace("Bearer ", "");
  
  if (!apiKey || !API_KEYS.has(apiKey)) {
    return c.json({ error: "Unauthorized" }, 401);
  }
  
  await next();
});
```

---

## 6. Security Hardening

### Input Validation

Every tool input must be validated. Never trust arguments from the LLM:

```typescript
import { z } from "zod";

// SQL injection prevention
const SafeIdSchema = z.string()
  .regex(/^[a-zA-Z0-9_-]+$/, "ID must be alphanumeric")
  .max(100);

// Path traversal prevention
const SafePathSchema = z.string()
  .refine(p => !p.includes(".."), "Path traversal not allowed")
  .refine(p => path.isAbsolute(p), "Must be absolute path");

// URL validation for fetch tools
const SafeUrlSchema = z.string()
  .url()
  .refine(u => {
    const url = new URL(u);
    return ["http:", "https:"].includes(url.protocol);
  }, "Only HTTP/HTTPS URLs allowed")
  .refine(u => {
    const url = new URL(u);
    const privateRanges = /^(10\.|172\.(1[6-9]|2[0-9]|3[01])\.|192\.168\.|127\.)/;
    return !privateRanges.test(url.hostname);
  }, "Private IP ranges not allowed");  // SSRF prevention
```

### Rate Limiting

```typescript
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(100, "1m"),  // 100 requests per minute
  analytics: true
});

app.use("/mcp", async (c, next) => {
  const identifier = c.get("user")?.sub || c.req.header("X-Forwarded-For") || "anonymous";
  const { success, limit, remaining } = await ratelimit.limit(identifier);
  
  if (!success) {
    return c.json({
      jsonrpc: "2.0",
      error: { code: -32000, message: "Rate limit exceeded" }
    }, 429);
  }
  
  c.header("X-RateLimit-Limit", String(limit));
  c.header("X-RateLimit-Remaining", String(remaining));
  await next();
});
```

### Tool Permission Scoping

Implement scope-based tool access control:

```typescript
const TOOL_PERMISSIONS: Record<string, string[]> = {
  "read_record": ["read", "write", "admin"],
  "create_record": ["write", "admin"],
  "delete_record": ["admin"],
  "manage_users": ["admin"]
};

function checkToolPermission(toolName: string, userScopes: string[]): void {
  const required = TOOL_PERMISSIONS[toolName];
  if (!required) return;  // Unknown tool — will fail at validation
  
  const hasPermission = required.some(scope => userScopes.includes(scope));
  if (!hasPermission) {
    throw new McpAuthError(
      `Insufficient permissions for tool '${toolName}'. Required: one of [${required.join(", ")}]`,
      403
    );
  }
}
```

### Prompt Injection Defense

LLMs can be manipulated via tool results. Implement:

1. **Input sanitization for tool results:** Strip control characters, limit length
2. **System prompt injection detection:** Flag results containing suspicious patterns
3. **Sandboxed tool execution:** Run code execution tools in isolated environments
4. **Output format validation:** Enforce expected structure for tool returns

```typescript
function sanitizeToolResult(result: string, maxLength = 50000): string {
  // Remove null bytes and other control characters
  const sanitized = result.replace(/[\x00-\x08\x0B\x0C\x0E-\x1F\x7F]/g, "");
  
  // Truncate to max length with indicator
  if (sanitized.length > maxLength) {
    return sanitized.slice(0, maxLength) + "\n[Content truncated]";
  }
  
  return sanitized;
}

// Detect potential prompt injection attempts
function detectInjection(content: string): boolean {
  const suspiciousPatterns = [
    /ignore previous instructions/i,
    /you are now/i,
    /system prompt/i,
    /\[INST\]/i,
    /<<<.*>>>/  // Common injection markers
  ];
  return suspiciousPatterns.some(p => p.test(content));
}
```

---

## 7. MCP Inspector — Debugging

### Running MCP Inspector

```bash
npx @modelcontextprotocol/inspector
```

This launches a web UI at `http://localhost:5173` that allows:
- Connecting to stdio or HTTP MCP servers
- Listing all tools, resources, and prompts
- Executing tools with custom arguments
- Viewing raw JSON-RPC messages
- Testing authentication flows

**Connecting to a stdio server:**
```bash
# In MCP Inspector UI:
Transport: stdio
Command: node dist/index.js
Args: (empty)
```

**Connecting to an HTTP server:**
```bash
# In MCP Inspector UI:
Transport: SSE/HTTP
URL: http://localhost:3000/mcp
```

### Common Debugging Patterns

**Tool not appearing:** Check that `server.registerTool` is called before `server.connect`. The capabilities are negotiated during the `initialize` handshake.

**Authentication failures:** Enable verbose logging; check that the Authorization header format matches expectations (Bearer token vs. API key header name).

**Tool returning empty results:** Add logging inside the tool handler; check that async/await is properly used; verify the database/API call is completing.

**Schema validation errors:** The LLM may pass arguments that don't match the schema. Test with MCP Inspector using edge case inputs.

---

## 8. Docker Deployment for Production

### Dockerfile (TypeScript MCP Server)

```dockerfile
FROM node:22-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY tsconfig.json ./
COPY src/ ./src/
RUN npm run build

FROM node:22-alpine AS runtime

# Security: non-root user
RUN addgroup -g 1001 -S mcp && adduser -S mcp -u 1001

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json ./

USER mcp

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "dist/index.js"]
```

### docker-compose.yml

```yaml
version: '3.8'
services:
  mcp-server:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - API_KEY=${API_KEY}
      - DATABASE_URL=${DATABASE_URL}
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mcp-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mcp-server
  template:
    metadata:
      labels:
        app: mcp-server
    spec:
      containers:
      - name: mcp-server
        image: myregistry/mcp-server:1.0.0
        ports:
        - containerPort: 3000
        env:
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: mcp-secrets
              key: api-key
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 30
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 10
```

---

## 9. Claude Desktop and IDE Integration

### claude_desktop_config.json

```json
{
  "mcpServers": {
    "my-local-server": {
      "command": "node",
      "args": ["/absolute/path/to/dist/index.js"],
      "env": {
        "API_KEY": "your-api-key-here"
      }
    },
    "my-python-server": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "env": {
        "DATABASE_URL": "sqlite:///data.db"
      }
    },
    "remote-server": {
      "transport": "http",
      "url": "https://mcp.myapp.com/mcp",
      "headers": {
        "Authorization": "Bearer ${REMOTE_TOKEN}"
      }
    }
  }
}
```

**Config location:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

### VS Code / Cursor Integration

**`.vscode/mcp.json` (VS Code Copilot):**
```json
{
  "mcpServers": {
    "project-tools": {
      "command": "node",
      "args": ["${workspaceFolder}/mcp-server/dist/index.js"]
    }
  }
}
```

### Programmatic MCP Client (Custom Agent)

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const transport = new StdioClientTransport({
  command: "node",
  args: ["./my-server/dist/index.js"]
});

const client = new Client({
  name: "my-agent",
  version: "1.0.0"
}, {
  capabilities: { tools: {} }
});

await client.connect(transport);

// List available tools
const { tools } = await client.listTools();
console.log("Available tools:", tools.map(t => t.name));

// Call a tool
const result = await client.callTool({
  name: "search_records",
  arguments: { query: "test", limit: 5 }
});

console.log(result.content);

await client.close();
```

---

## 10. MCP Ecosystem and 500+ Servers

### Official Servers (Anthropic / Model Context Protocol org)

| Server | Capability | Use Case |
|---|---|---|
| filesystem | Local file read/write | Document access |
| github | GitHub API | Code repository operations |
| gitlab | GitLab API | Code repository (GitLab) |
| google-maps | Places, directions | Location-based tools |
| slack | Messages, channels | Team communication |
| postgres | Database queries | Data access |
| sqlite | SQLite operations | Local data |
| puppeteer | Browser automation | Web scraping, testing |
| brave-search | Web search | Research tools |

### Finding MCP Servers

- `mcp.so` — Community server directory
- GitHub topic: `mcp-server`
- Smithery — MCP server marketplace
- Official list: `github.com/modelcontextprotocol/servers`

### Building a Server Registry (Internal)

For organizations with multiple internal MCP servers, implement a discovery registry:

```typescript
// Internal server registry
const SERVER_REGISTRY = {
  "customer-db": {
    url: "https://mcp.internal.company.com/customers",
    description: "Customer database access",
    version: "2.1.0",
    scopes: ["read", "write"]
  },
  "analytics": {
    url: "https://mcp.internal.company.com/analytics",
    description: "Business analytics queries",
    version: "1.5.0",
    scopes: ["read"]
  }
};
```

---

## 11. The 10 Rules for Production MCP Servers

1. **Stateless HTTP transport is the production standard.** State in servers = scaling problems. Use external storage for session state.
2. **Validate every tool input.** The LLM will pass unexpected values. Validate with Zod (TypeScript) or Pydantic (Python) before any processing.
3. **Rate limit every endpoint.** An AI agent in a loop can send thousands of requests. Protect both your server and downstream APIs.
4. **SSRF prevention for any fetch tool.** Validate URLs against private IP ranges before making HTTP requests.
5. **OAuth 2.1 with PKCE for multi-user servers.** API keys are fine for developer tools; OAuth is required for user-facing servers.
6. **Tool descriptions are load-bearing.** Poor descriptions = tool not called or called incorrectly. Treat them like documentation.
7. **MCP Inspector before client integration.** Debug your server in isolation before testing with Claude Desktop or an agent.
8. **Return both text and structuredContent.** Modern clients prefer structured data; legacy clients need text. Return both for maximum compatibility.
9. **Health endpoint is non-negotiable for production.** Every deployed MCP server needs a `/health` route for load balancer and monitoring checks.
10. **MCP is the integration standard.** Build internal tools as MCP servers once; they're usable by any MCP-compatible client without per-client integration work.
