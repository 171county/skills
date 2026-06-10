---
name: mcp-expert
description: Use this skill when you need expert-level knowledge about Model Context Protocol (MCP). Triggers on questions about: MCP specification (primitives, transports, authorization), building MCP servers (Python FastMCP, TypeScript SDK), building MCP clients, MCP security vulnerabilities and best practices, MCP vs. function calling comparison, MCP governance (Linux Foundation AAIF donation), MCP server discovery and the official registry, OAuth 2.1 + PKCE authentication for MCP, Streamable HTTP transport, MCP Tasks abstraction (November 2025 spec), MCP in production deployment patterns, and debugging MCP implementations.
---

# MCP Expert

## MCP Overview and History

### What is MCP?
Model Context Protocol (MCP) is an open standard that enables AI models to securely connect to external data sources and tools. Think of it as "USB-C for AI" — a universal connector that allows any AI application (host) to connect to any data source or tool (server) using a single standardized protocol.

**Origin:** Anthropic invented and open-sourced MCP in November 2024.

**Governance milestone:** In December 2025, Anthropic donated MCP to the Linux Foundation's AI & Data Foundation (AAIF). Co-founders of AAIF MCP working group: Anthropic, Block (Square parent), OpenAI. Broad adoption of the spec was accelerating.

**Why MCP matters:**
Before MCP: Every AI application needed custom integrations for every data source. N applications × M data sources = N×M custom integrations.
After MCP: N applications + M servers = N+M integrations via shared protocol.

### MCP Current Specification (2025-11-25)
The latest stable spec version is `2025-11-25` (released November 2025).

Previous spec: `2024-11-05` (original release)

**Transport change history:**
- November 2024: stdio + HTTP + SSE (two separate HTTP transports)
- March 2025: HTTP+SSE deprecated → replaced by Streamable HTTP (single unified HTTP transport)
- Current: stdio (local) + Streamable HTTP (remote) are the two supported transports

---

## MCP Architecture

### Protocol Foundation
MCP is built on **JSON-RPC 2.0** over a chosen transport layer.

**JSON-RPC 2.0 message types:**
```json
// Request
{
  "jsonrpc": "2.0",
  "id": "req-123",
  "method": "tools/call",
  "params": {
    "name": "search_web",
    "arguments": {"query": "MCP specification 2025"}
  }
}

// Response
{
  "jsonrpc": "2.0",
  "id": "req-123",
  "result": {
    "content": [{"type": "text", "text": "Search results..."}]
  }
}

// Notification (no response expected)
{
  "jsonrpc": "2.0",
  "method": "notifications/tools/list_changed"
}
```

### Connection Lifecycle
1. **Initialize**: Client sends `initialize` with protocol version and capabilities
2. **Initialized notification**: Client sends after processing server's initialize response
3. **Normal operation**: Request/response and notifications exchanged
4. **Shutdown**: Either party can close the connection

```
Client                     Server
  |                          |
  |--- initialize ---------->|
  |<-- initialize response---|
  |--- initialized ---------->|
  |                          |
  |--- tools/list ---------->|
  |<-- tools/list result ----|
  |                          |
  |--- tools/call ---------->|
  |<-- tools/call result ----|
  |                          |
  |--- close ---------------x|
```

---

## The Six MCP Primitives

### 1. Tools
Functions the model can invoke to perform actions or retrieve data.

**Server-side tool definition:**
```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def search_database(query: str, limit: int = 10) -> list[dict]:
    """Search the company database for records matching the query.
    
    Args:
        query: Search terms to match against records
        limit: Maximum number of results to return
    """
    return db.search(query, limit=limit)
```

**What the model receives:**
```json
{
  "name": "search_database",
  "description": "Search the company database for records matching the query.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {"type": "string", "description": "Search terms"},
      "limit": {"type": "integer", "description": "Maximum results", "default": 10}
    },
    "required": ["query"]
  }
}
```

**Tool result types:**
- `TextContent`: Plain text
- `ImageContent`: Base64 image
- `EmbeddedResource`: A resource URI
- `isError: true`: Tool execution failed (model handles gracefully)

### 2. Resources
Static or dynamic data sources the model can read (application-controlled, not model-controlled).

**Resource types:**
- Static: Files, documentation, configuration
- Dynamic: Database query results, API data, user-specific data

**URI scheme examples:**
- `file:///path/to/document.pdf`
- `postgres://db/table/schema`
- `github://owner/repo/path/to/file`
- Custom: `myapp://entity/{id}`

```python
@mcp.resource("file://{path}")
def read_file(path: str) -> str:
    """Read a file from the filesystem"""
    with open(path) as f:
        return f.read()

@mcp.resource("db://users/{user_id}/profile")
def get_user_profile(user_id: str) -> dict:
    """Get user profile data"""
    return db.users.get(user_id)
```

**Resource templates (URI templates per RFC 6570):**
- Define parameterized resource URIs
- Client expands templates with actual values
- Enables dynamic resource sets

### 3. Prompts
Predefined prompt templates the model can use or expose to users.

```python
@mcp.prompt()
def code_review_prompt(language: str, code: str) -> list[Message]:
    """Generate a code review prompt"""
    return [
        Message(role="user", content=f"""Review this {language} code:
        
```{language}
{code}
```

Check for: bugs, security issues, performance, readability.""")
    ]
```

**Use cases:**
- Standardized task prompts (code review, document summarization)
- Complex multi-turn prompt templates
- Organization-specific prompt libraries

### 4. Sampling
Server-initiated requests for model completion — the server asks the host to run an inference.

**Unique aspect:** Most MCP primitives are client→server. Sampling is server→client.

```python
# Server requests the model to generate something
async def generate_analysis(data: dict) -> str:
    result = await mcp.sample(
        messages=[{
            "role": "user",
            "content": f"Analyze this data and identify anomalies: {json.dumps(data)}"
        }],
        model_preferences={"hints": ["claude-sonnet"]},
        max_tokens=1000
    )
    return result.content[0].text
```

**Why sampling matters:** Enables servers to use the connected AI model's intelligence for their own processing, without needing a separate API key or model access.

### 5. Roots
Filesystem or URL roots that the client exposes to the server — defines the workspace.

```json
{
  "method": "roots/list",
  "result": {
    "roots": [
      {
        "uri": "file:///home/user/project",
        "name": "Project Root"
      }
    ]
  }
}
```

**Use cases:** IDE integrations, local file access, workspace-scoped operations.

### 6. Elicitation (2025-11-25 Spec)
Server requests additional information from the user via the client.

```python
# Server needs user input during execution
result = await mcp.elicit(
    message="Please provide your API key to continue",
    schema={
        "type": "object",
        "properties": {
            "api_key": {"type": "string", "title": "API Key", "format": "password"}
        },
        "required": ["api_key"]
    }
)
api_key = result.content["api_key"]
```

**Why elicitation:** Enables servers to request structured user input mid-operation without requiring pre-configuration of all parameters.

---

## Transport Layer

### stdio Transport (Local)
Standard input/output — the client spawns the server as a subprocess.

```json
// Claude Desktop config (~/.config/claude/config.json)
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/docs"],
      "env": {"NODE_ENV": "production"}
    },
    "myserver": {
      "command": "python",
      "args": ["-m", "myserver"],
      "env": {"DATABASE_URL": "postgresql://..."}
    }
  }
}
```

**When to use stdio:**
- Local tools (filesystem access, local database, local APIs)
- Development and testing
- Security-sensitive operations (no network exposure)
- Claude Desktop integrations

**Security advantage:** No network exposure. Server process runs as user. No authentication needed (user's local permissions apply).

### Streamable HTTP Transport (Remote)
Single endpoint, supports both request/response and server-sent events (SSE) for streaming.

**Endpoint design:**
- Single POST endpoint (e.g., `/mcp`)
- Client sends JSON-RPC requests as POST body
- Server can respond:
  - Direct JSON response (for simple request/response)
  - `text/event-stream` for streaming responses or notifications

```python
# FastMCP HTTP server
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("remote-server")

@mcp.tool()
def fetch_data(url: str) -> str:
    import httpx
    return httpx.get(url).text

if __name__ == "__main__":
    mcp.run(transport="http", host="0.0.0.0", port=8000)
```

**Client connection:**
```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async with streamablehttp_client("https://myserver.example.com/mcp") as (read, write, _):
    async with ClientSession(read, write) as session:
        await session.initialize()
        tools = await session.list_tools()
```

---

## Authentication and Authorization

### OAuth 2.1 + PKCE (Mandatory Standard)
MCP requires OAuth 2.1 with PKCE (Proof Key for Code Exchange) for remote server authentication.

**Why OAuth 2.1:**
- Security: Eliminates implicit flow vulnerabilities
- PKCE: Prevents authorization code interception attacks
- Industry standard: Builds on existing OAuth 2.0 tooling

**PKCE Flow:**
```
1. Client generates code_verifier (random 43-128 char string)
2. Client computes code_challenge = BASE64URL(SHA256(code_verifier))
3. Client sends authorization request with code_challenge
4. User authorizes → server issues authorization code
5. Client exchanges code + code_verifier for access token
6. Server verifies SHA256(code_verifier) == code_challenge
```

**MCP OAuth implementation:**
```python
# Server-side: FastMCP with OAuth
from mcp.server.fastmcp import FastMCP
from mcp.server.auth import OAuthProvider

class MyOAuthProvider(OAuthProvider):
    async def validate_token(self, token: str) -> dict:
        # Validate JWT or call your auth service
        return jwt.decode(token, secret, algorithms=["RS256"])
    
    async def get_scopes(self, token_claims: dict) -> list[str]:
        return token_claims.get("scope", "").split()

mcp = FastMCP("my-server", oauth_provider=MyOAuthProvider())
```

**Security finding (2025):** Security audit of 518 publicly available MCP servers found:
- 41% had NO authentication (accepting any connection)
- 59% implemented some auth
- Of those with auth, only 23% used OAuth 2.1 with PKCE

---

## Building MCP Servers

### Python: FastMCP Framework

```python
from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel

mcp = FastMCP(
    name="company-data-server",
    version="1.0.0",
    description="Access company data and analytics"
)

# Tool with type annotations (FastMCP auto-generates JSON Schema)
@mcp.tool()
async def get_customer(customer_id: str, include_history: bool = False) -> dict:
    """Retrieve customer record by ID.
    
    Args:
        customer_id: UUID of the customer
        include_history: Whether to include purchase history
    
    Returns:
        Customer object with requested data
    """
    customer = await db.customers.get(customer_id)
    if include_history:
        customer["history"] = await db.orders.get_by_customer(customer_id)
    return customer

# Resource
@mcp.resource("customers://list")
async def list_customers() -> list[dict]:
    """List all customers"""
    return await db.customers.list(limit=100)

# Prompt
@mcp.prompt()
def customer_analysis_prompt(customer_id: str) -> str:
    return f"Analyze the purchasing patterns for customer {customer_id} and identify upsell opportunities."

# Run
if __name__ == "__main__":
    mcp.run()  # stdio by default; mcp.run(transport="http") for HTTP
```

### TypeScript: Official MCP SDK

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "company-data-server",
  version: "1.0.0",
});

// Tool registration
server.tool(
  "get_customer",
  "Retrieve customer record by ID",
  {
    customer_id: z.string().describe("UUID of the customer"),
    include_history: z.boolean().optional().default(false)
  },
  async ({ customer_id, include_history }) => {
    const customer = await db.customers.get(customer_id);
    if (include_history) {
      customer.history = await db.orders.getByCustomer(customer_id);
    }
    return {
      content: [{
        type: "text",
        text: JSON.stringify(customer, null, 2)
      }]
    };
  }
);

// Resource
server.resource(
  "customers://list",
  "List all customers",
  async (uri) => {
    const customers = await db.customers.list({ limit: 100 });
    return {
      contents: [{
        uri: uri.toString(),
        text: JSON.stringify(customers, null, 2),
        mimeType: "application/json"
      }]
    };
  }
);

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Server Testing

```python
# Unit testing MCP tools
import pytest
from mcp.server.fastmcp import FastMCP
from mcp.shared.memory import create_client_server_memory_streams

async def test_get_customer():
    # Create in-memory transport for testing
    async with create_client_server_memory_streams() as (client_streams, server_streams):
        # ... test tool invocation
        pass

# Integration testing with MCP Inspector
# Run: npx @modelcontextprotocol/inspector python -m myserver
```

**MCP Inspector:** Official debugging tool. Launches a web UI to interactively test your MCP server — call tools, browse resources, inspect prompts without a full client.

---

## Building MCP Clients

### Python Client

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

# Connect to local server
server_params = StdioServerParameters(
    command="python",
    args=["-m", "myserver"],
    env={"DATABASE_URL": os.environ["DATABASE_URL"]}
)

async with stdio_client(server_params) as (read, write):
    async with ClientSession(read, write) as session:
        # Initialize
        await session.initialize()
        
        # List available tools
        tools_result = await session.list_tools()
        tools = tools_result.tools
        
        # Call a tool
        result = await session.call_tool(
            "get_customer",
            arguments={"customer_id": "uuid-123", "include_history": True}
        )
        
        # Read a resource
        resource_result = await session.read_resource("customers://list")
```

### Integration with Claude API

```python
import anthropic
from mcp import ClientSession
from mcp.client.stdio import stdio_client

async def run_with_mcp_tools():
    client = anthropic.Anthropic()
    
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as session:
            await session.initialize()
            
            # Get tools in Anthropic format
            mcp_tools = await session.list_tools()
            anthropic_tools = [
                {
                    "name": tool.name,
                    "description": tool.description,
                    "input_schema": tool.inputSchema
                }
                for tool in mcp_tools.tools
            ]
            
            # Agentic loop
            messages = [{"role": "user", "content": "Get the customer with ID abc-123"}]
            
            while True:
                response = client.messages.create(
                    model="claude-sonnet-4-6",
                    max_tokens=4096,
                    tools=anthropic_tools,
                    messages=messages
                )
                
                if response.stop_reason == "end_turn":
                    break
                
                if response.stop_reason == "tool_use":
                    tool_results = []
                    for block in response.content:
                        if block.type == "tool_use":
                            result = await session.call_tool(block.name, block.input)
                            tool_results.append({
                                "type": "tool_result",
                                "tool_use_id": block.id,
                                "content": result.content[0].text
                            })
                    
                    messages.append({"role": "assistant", "content": response.content})
                    messages.append({"role": "user", "content": tool_results})
```

---

## MCP vs. Alternatives

### MCP vs. OpenAI Function Calling / Tool Use

| Dimension | MCP | Direct Tool Use |
|-----------|-----|----------------|
| Reusability | Server reusable across apps | Tools defined per-application |
| Standardization | Universal protocol | Provider-specific format |
| Discovery | Server advertises tools at runtime | Developer defines tools at code time |
| Resources | First-class concept (data sources) | Not standardized |
| Transport | stdio or HTTP | API calls (always HTTP) |
| Multi-model | Works with any MCP client | Provider-specific |
| Setup | Server process required | Just code |
| Best for | Reusable integrations, large tool ecosystems | Simple, app-specific tools |

### MCP vs. LangChain Tools

| Dimension | MCP | LangChain Tools |
|-----------|-----|----------------|
| Protocol | Standardized spec | Python/JavaScript library |
| Server/client | Explicit separation | Single-process |
| Language | Any (stdio/HTTP) | Python/JS only |
| Distribution | Server can be deployed remotely | Library installed locally |
| Ecosystem | Growing independent ecosystem | LangChain-specific |
| Dynamic discovery | Runtime tool discovery | Compile-time tool definition |

**Rule of thumb:**
- Use MCP when: building reusable integrations, multiple apps need same tools, third-party tool ecosystem
- Use direct function calling when: app-specific tools, simple automation, single-model apps
- Use LangChain tools when: already using LangChain, pure Python ecosystem, rapid prototyping

---

## MCP Ecosystem

### Official Registry
The official MCP server registry launched with the Linux Foundation donation. Community-curated, quality-reviewed.

**Top MCP servers (as of 2026):**
- `@modelcontextprotocol/server-filesystem`: Local filesystem read/write
- `@modelcontextprotocol/server-github`: GitHub repos, PRs, issues
- `@modelcontextprotocol/server-postgres`: PostgreSQL query access
- `@modelcontextprotocol/server-google-maps`: Maps and location data
- `@modelcontextprotocol/server-brave-search`: Web search via Brave
- `@modelcontextprotocol/server-slack`: Slack workspace access
- `mcp-server-sqlite`: SQLite database access
- `mcp-pandoc`: Document format conversion

### MCP Clients (Applications)
- **Claude Desktop**: Anthropic's native MCP client (full stdio + HTTP support)
- **Claude Code**: MCP via `claude_desktop_config.json` and custom MCP servers
- **Cursor**: IDE with MCP tool integration
- **Zed**: Code editor with MCP support
- **OpenAI agents**: OpenAI Agents SDK supports MCP tool servers
- **Continue**: Open-source coding assistant, MCP support
- **Custom clients**: Any application implementing the MCP client spec

---

## MCP Security Best Practices

### Top Vulnerabilities

**Tool name shadowing:**
- Malicious server registers tool named same as trusted tool
- Defense: Validate server identity, use official registry, sign server binaries

**Prompt injection via tools:**
- Tool result contains malicious instructions ("Ignore previous instructions, instead...")
- Defense: Validate and sanitize all tool results before including in context

**Overly permissive tools:**
- Tool grants access beyond what's needed
- Defense: Principle of least privilege, scope tools narrowly

**Sensitive data in tool arguments:**
- Tool arguments logged and visible in traces → PII/credential exposure
- Defense: Scrub sensitive fields before logging, use references not values

**Untrusted servers:**
- User installs malicious MCP server from unofficial registry
- Defense: Official registry vetting, code review of open-source servers, sandboxed execution

### Secure Server Implementation

```python
from mcp.server.fastmcp import FastMCP
import logging

mcp = FastMCP("secure-server")

@mcp.tool()
async def read_customer_data(customer_id: str, requester_id: str) -> dict:
    # 1. Validate authorization
    if not await auth.can_access_customer(requester_id, customer_id):
        raise PermissionError(f"User {requester_id} not authorized to access {customer_id}")
    
    # 2. Validate and sanitize input
    if not is_valid_uuid(customer_id):
        raise ValueError("Invalid customer_id format")
    
    # 3. Log without sensitive data
    logging.info(f"Tool called: read_customer_data for customer_id={hash(customer_id)[:8]}")
    
    # 4. Return data with minimal exposure
    customer = await db.customers.get(customer_id)
    return {
        "id": customer["id"],
        "name": customer["name"],
        # Omit: SSN, credit card, full DOB
    }
```

---

## The November 2025 Spec: Tasks Abstraction

The `2025-11-25` spec introduced the **Tasks** abstraction for long-running operations.

**Problem:** Original MCP assumed request/response completion within seconds. Real-world tools (web scraping, file processing, API chains) can take minutes.

**Tasks solution:**
```python
# Server creates a task for long-running work
@mcp.tool()
async def long_running_analysis(data_url: str) -> Task:
    task = mcp.create_task()
    
    # Start background processing
    asyncio.create_task(process_and_update(task, data_url))
    
    return task  # Client receives task handle immediately

async def process_and_update(task: Task, data_url: str):
    await task.update_progress(0.1, "Fetching data...")
    data = await fetch_data(data_url)
    
    await task.update_progress(0.5, "Analyzing...")
    result = await analyze(data)
    
    await task.complete(result=result)
```

**Client-side task polling:**
```python
task = await session.call_tool("long_running_analysis", {"data_url": "..."})
task_id = task.task_id

# Poll for completion
while True:
    status = await session.get_task(task_id)
    if status.state == "completed":
        print(status.result)
        break
    elif status.state == "failed":
        raise Exception(status.error)
    await asyncio.sleep(2)
```
