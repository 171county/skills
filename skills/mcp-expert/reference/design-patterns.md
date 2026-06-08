# MCP Design Patterns & Best Practices

## Tool Design

### Naming Conventions
Tools names must be **globally unique across all servers a client connects to** — clients typically prefix tool names with the server name, but you should not depend on that.

**Canonical Pattern:** `{service}_{verb}_{noun}`
- `github_create_issue` not `create_issue`
- `slack_send_message` not `send_message`
- `stripe_charge_customer` not `charge`

**Rules:**
- Maximum **64 characters** for the fully qualified name (some clients add prefixes/suffixes)
- Must start with a letter
- Alphanumeric, underscores, hyphens only
- Case-sensitive; be consistent within a server
- Snake_case is standard (aligns with Python/JSON conventions)

**Action Verbs:** Use consistent vocabulary across your server:
- Read operations: `get_`, `list_`, `search_`, `fetch_`, `read_`
- Write operations: `create_`, `update_`, `patch_`, `set_`
- Delete operations: `delete_`, `remove_`, `archive_`
- Execute operations: `run_`, `trigger_`, `execute_`, `send_`

### Tool Description Quality
The description is the most important field — it's what the model reads to decide whether to use this tool. **Bad descriptions cause tool misuse more often than bad schemas.**

**Description Template:**
```
[One sentence: what it does]. [One sentence: when to use it vs similar tools].
[Optional: what it returns]. [Optional: important constraints or side effects].
```

**Example (Good):**
```
Searches GitHub issues by keyword and filters. Use this to find existing issues 
before creating new ones to avoid duplicates. Returns issue title, number, URL, 
status, and label list. Searches both title and body content. 
```

**Example (Bad):**
```
Search issues
```

**Anti-patterns to avoid:**
- Vague verbs: "process", "handle", "manage" — be specific
- Missing distinction from similar tools: if you have both `search_issues` and `list_issues`, explain when to use each
- Missing return type hints: model can't construct correct follow-up calls without knowing what you return
- False precision: don't claim "returns all matching issues" if you paginate

### Schema Design
Use **JSON Schema** (or Zod/Pydantic) for all tool inputs. The schema is the model's contract.

**Required fields:** Mark truly required fields as `required`. Don't over-require — optional fields with good defaults improve model experience.

**Field Descriptions:** Write field descriptions as instructions to the model, not documentation for humans:
```json
{
  "path": {
    "type": "string",
    "description": "Absolute file path. Must start with /. Use the workspace root from context if relative paths are needed. Example: /home/user/project/src/main.py"
  }
}
```

**Enums over free-text when possible:**
```json
{
  "state": {
    "type": "string",
    "enum": ["open", "closed", "all"],
    "default": "open",
    "description": "Filter by issue state. Use 'all' to include closed issues."
  }
}
```

**Avoid overly nested schemas:** Flat is better. Deep nesting makes it harder for models to construct correct calls. If you need nested data, consider splitting into multiple tools.

### Tool Annotations
Always provide annotations — they enable client UIs to show users what a tool will do before approving:

```typescript
server.registerTool("delete_file", {
  description: "Permanently deletes a file from the filesystem",
  inputSchema: { ... },
  annotations: {
    readOnlyHint: false,
    destructiveHint: true,     // enables confirmation dialogs in clients
    idempotentHint: false,
    openWorldHint: false
  }
});
```

### Granularity: Atomic vs Workflow Tools
**Atomic tools** (single operations) give models maximum flexibility but require more reasoning steps. **Workflow tools** (multi-step operations wrapped as one) reduce complexity for common patterns but reduce flexibility.

**Recommendation:**
1. Start with comprehensive atomic coverage of the underlying API
2. Add workflow tools for the **3-5 most common complex operations**
3. Never add a workflow tool that can't be decomposed into your atomic tools
4. Document which atomic tools a workflow tool combines

**Example:** For a GitHub server:
- Atomic: `github_list_prs`, `github_get_pr`, `github_list_pr_files`, `github_list_pr_comments`
- Workflow: `github_summarize_pr` (calls all the above and formats a report)

---

## Resource Design

### URI Scheme Architecture
Design your URI scheme to reflect the domain's natural hierarchy:

```
{scheme}://{authority}/{resource-type}/{identifier}[/{sub-resource}]
```

Examples:
- `github://repos/anthropic/claude/issues/123`
- `db://prod/tables/users/schema`
- `config://app/database/connection`
- `docs://api-reference/authentication`

**Don't reuse HTTP URLs as resource URIs** — it creates ambiguity between fetching the MCP resource and the web resource. Use your own scheme.

### Static vs Dynamic Resources
**Static resources** have stable URIs and content that changes infrequently. Cache aggressively.
```json
{ "uri": "config://app/settings", "name": "Application Settings", "mimeType": "application/json" }
```

**Dynamic resources** use URI templates (RFC 6570) for parameterized access:
```json
{ "uriTemplate": "github://repos/{owner}/{repo}/issues/{number}", "name": "GitHub Issue", "mimeType": "application/json" }
```

With URI templates, implement the **completion API** (`completion/complete`) so clients can provide auto-complete when users type resource URIs.

### Resource Content Types
Use MIME types to signal how clients should handle resource content:
- `text/plain` — plain text
- `text/markdown` — markdown-formatted text
- `application/json` — structured JSON data
- `text/html` — HTML content
- `image/png`, `image/jpeg` — binary images (returned as base64)

### Resource Subscriptions
For resources that change frequently, implement subscriptions:
1. Client calls `resources/subscribe` with the resource URI
2. Server sends `notifications/resources/updated` when content changes
3. Client re-reads the resource to get updated content
4. Client calls `resources/unsubscribe` when done

**When to implement subscriptions:**
- Log streams
- Dashboard metrics
- Live database views
- Message queues

### The Resources-as-Context Pattern
A powerful pattern: expose large context as resources, inject them via prompts, rather than returning everything from tools.

```
Tool: github_get_repo_info → returns 50 fields
Resource: github://repos/owner/repo/context → returns pre-formatted context for AI reasoning
Prompt: analyze_repository → injects the resource + asks the model to analyze
```

This separates data retrieval (resource) from analysis (tool/prompt), enabling caching and reuse.

---

## Prompt Design

### When to Use Prompts vs Tools
- **Prompts**: Complex, multi-step workflows with a fixed starting shape that users initiate. Domain expert workflows ("do a code review", "analyze this data", "write a PR description").
- **Tools**: Individual operations the model decides to invoke during autonomous reasoning.
- **Rule of thumb**: If a human would pick it from a menu, it's a prompt. If the model decides when to call it, it's a tool.

### Prompt Structure Best Practices
Prompts should return a `messages` array ready to inject into the conversation. Include:
1. **System context**: Role definition and constraints
2. **Resource injection**: Pre-loaded relevant resources
3. **Task specification**: Clear instructions
4. **Output format**: Expected structure of the response

```python
@mcp.prompt()
async def review_pull_request(owner: str, repo: str, pr_number: int) -> list[Message]:
    pr_data = await fetch_pr(owner, repo, pr_number)
    return [
        {
            "role": "user",
            "content": f"""Please review this pull request:

**PR:** {pr_data['title']} (#{pr_number})
**Changes:** {pr_data['additions']} additions, {pr_data['deletions']} deletions
**Files changed:** {len(pr_data['files'])}

{format_diff(pr_data['diff'])}

Provide:
1. Summary of changes
2. Potential bugs or issues
3. Code quality feedback
4. Security considerations
5. Approve/Request Changes recommendation"""
        }
    ]
```

---

## Multi-Server Composition

### The MCP Client Architecture
Hosts (Claude Desktop, Claude Code, Cursor, VS Code) can connect to **multiple MCP servers simultaneously**. Each server's tools are namespaced and appear as a combined toolset to the model.

```
Claude Desktop
├── github-mcp → github_create_issue, github_list_prs, ...
├── slack-mcp → slack_send_message, slack_list_channels, ...
├── filesystem-mcp → read_file, write_file, ...
└── memory-mcp → store_memory, recall_memory, ...
```

**Design Principle:** Each server should be **independently useful** and handle a **single domain**. Avoid cross-domain tools in one server (e.g., a server that does both GitHub AND Slack operations — split them).

### Tool Naming Across Multiple Servers
When multiple servers are loaded, tool names are typically exposed as `{server_name}__{tool_name}` or similar by the client. However, **you should not depend on client-side namespacing** — always include your service name in the tool name itself (`github_create_issue`, not `create_issue`).

### The Gateway / Proxy Pattern
For enterprise deployments, a single MCP gateway server can proxy multiple backend services:

```
AI Client → MCP Gateway Server → {Internal Service A, Internal Service B, ...}
```

Benefits:
- Single auth point (OAuth 2.1 at the gateway)
- Centralized audit logging
- Rate limiting at one location
- Ability to swap underlying services without changing client config

The Red Hat MCP Gateway pattern implements this with full auth, RBAC, and observability.

### Conflict Resolution
If two servers expose tools with the same effective purpose (e.g., two different search servers), document the disambiguation strategy in each tool's description. The model will use descriptions to choose. Don't rely on name collision avoidance alone.

---

## Stateful vs Stateless Server Design

### Stateless Server Design (Recommended for HTTP)
Each request is self-contained. No server-side session state between tool calls.

**Benefits:**
- Scales horizontally behind any load balancer
- No session affinity required (especially after 2026 RC)
- Resilient to server restarts
- Simple to test

**Patterns for maintaining logical state:**
- Pass state as tool parameters (explicit handle pattern)
- Store state in the client's conversation context via memory tools
- Use external storage (Redis, database) with IDs passed in tool calls

```python
# Stateless pattern: client passes conversation_id for context
@mcp.tool()
async def get_analysis_context(conversation_id: str) -> str:
    return await redis.get(f"context:{conversation_id}")
```

### Stateful Server Design (Necessary for Some Use Cases)
Some operations genuinely require server-side state:
- Long-running async workflows tracked by task ID
- Open file handles or database transactions
- Interactive sessions (e.g., a REPL or debug session)
- Browser automation sessions (Puppeteer/Playwright)

For stateful servers using HTTP transport, use sticky sessions (client is routed to same server instance) until the 2026 RC eliminates this complexity.

**Lifecycle management:** Always provide explicit `close_session`, `end_task`, or equivalent tools so the model can clean up state when done. Don't rely on server-side timeouts alone.

### The "Toolhost" Pattern
An advanced composition pattern where a MCP server acts as a dynamic tool registry, loading and exposing tools from multiple underlying services at runtime based on the current context. The server itself doesn't implement the tools — it delegates to other services and routes tool calls.

Use cases:
- Enterprise tool catalogs where a user's available tools depend on their role
- Multi-tenant SaaS where each tenant has different enabled integrations
- AI agent platforms that load tools on demand

---

## Response Design

### Content Types
Tools return `content` arrays. Each item is typed:

```json
{
  "content": [
    { "type": "text", "text": "Found 3 matching issues" },
    { "type": "image", "data": "<base64>", "mimeType": "image/png" },
    { "type": "resource", "resource": { "uri": "github://...", "text": "..." } }
  ]
}
```

### Response Size Management
- **Paginate** all list operations. Return `has_more`, `next_cursor`, `total_count`.
- **Truncate large text**: Return summaries or excerpts, with a tool to fetch the full content
- **Dual format**: Support both `json` and `markdown` response formats
- **Default page size**: 20-50 items

### Error Messages as Instructions
```python
# Bad
raise MCPError("File not found")

# Good  
return {
    "isError": True,
    "content": [{ "type": "text", "text": 
        "File not found: /path/to/file. "
        "Use list_directory('/path/to') to see available files, "
        "or check if the path is correct with get_working_directory()."
    }]
}
```

### Structured Content (June 2025+)
When your tool has a defined `outputSchema`, return both text content AND structured data:

```typescript
return {
    content: [{ type: "text", text: formatAsMarkdown(result) }],
    structuredContent: result  // typed JSON matching outputSchema
};
```

Clients that support structured content can use the typed data for programmatic processing, while older clients fall back to the text representation.
