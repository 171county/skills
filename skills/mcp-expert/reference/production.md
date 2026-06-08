# Production MCP Deployment Guide

## Deployment Architecture Decisions

### Local vs Remote Servers

**Local MCP Servers (stdio)**
- Run as subprocess on user's machine
- Zero network latency for IPC
- No hosting costs
- Per-user deployment (each user installs on their machine)
- Cannot be shared across teams without each person installing
- Sensitive credentials stay on user's machine (good for personal tokens)
- Desktop Extensions make installation one-click for Claude Desktop users

**Remote MCP Servers (Streamable HTTP)**
- Hosted as a cloud service or internal microservice
- Multiple clients can connect simultaneously
- Centralized deployment, updates, and configuration
- Centralized credential management (no per-user secret management)
- Requires auth infrastructure (OAuth 2.1)
- Teams share one deployment rather than each managing their own
- **Sticky sessions required** until 2026 RC makes protocol stateless

### Choosing Hosting Infrastructure

| Use Case | Recommended Approach |
|----------|---------------------|
| Personal/developer tool | Local stdio, install via npm/pip |
| Team tool (< 50 users) | Single server VM or container, Streamable HTTP |
| Enterprise tool (> 50 users) | Container cluster behind load balancer with sticky sessions |
| SaaS product with MCP | Multi-tenant remote server, OAuth 2.1, connection pooling |
| High-frequency tool calls | Consider co-location with the data source to minimize latency |

### Cloud Platform Options

**Container Services:**
- AWS ECS/Fargate — good for auto-scaling, native ALB integration with sticky sessions
- Google Cloud Run — serverless containers, but limited persistent connection support (better after 2026 RC stateless spec)
- Azure Container Instances — simple deployment, integrates with Azure AD for auth

**Kubernetes:**
- Best for large-scale enterprise deployments
- Use session affinity annotations for sticky routing (until stateless spec)
- Horizontal Pod Autoscaler for tool-call-based scaling

**Serverless Considerations:**
- Lambda/Cloud Functions are problematic for current (stateful) MCP — cold starts break session continuity
- After the 2026-07-28 RC stateless spec, serverless becomes viable for MCP
- Until then, use always-warm containers for production

---

## Scaling MCP Servers

### Current (2025-11-25 Spec): Session-Aware Scaling

Until the 2026 stateless spec, remote MCP servers must route requests from the same client to the same server instance.

**Sticky Session Implementation:**
```nginx
# NGINX: ip_hash for sticky sessions
upstream mcp_servers {
    ip_hash;
    server mcp-1.internal:8080;
    server mcp-2.internal:8080;
    server mcp-3.internal:8080;
}
```

**ALB (AWS): Session stickiness with Mcp-Session-Id:**
The `Mcp-Session-Id` header is the natural sticky key. Configure your load balancer to route based on this header:
```
Target Group → Stickiness: Cookie-based or Header-based (Mcp-Session-Id)
Duration: Match your session timeout (default: 30 minutes)
```

**Redis Session Store Pattern:**
For resilience (if a server instance fails, sessions can be resumed on another instance):
```python
# Store serializable session state in Redis
async def get_session(session_id: str) -> dict:
    raw = await redis.get(f"mcp:session:{session_id}")
    return json.loads(raw) if raw else {}

async def save_session(session_id: str, state: dict, ttl: int = 1800):
    await redis.setex(f"mcp:session:{session_id}", ttl, json.dumps(state))
```

### Future (2026 RC): Stateless Scaling

Once clients migrate to the 2026-07-28 spec:
- No sticky sessions needed
- Run behind any round-robin load balancer
- Serverless/ephemeral compute becomes viable
- Horizontal scaling trivially easy
- State stored explicitly in external storage (Redis, DB), passed via tool parameters

**Migration Path:** Design stateless-first today even if current spec requires sessions. Use the session store pattern so migration to stateless is a configuration change, not a refactor.

---

## Testing MCP Servers

### MCP Inspector (Official Tool)
`github.com/modelcontextprotocol/inspector`

The official interactive debugging UI. Run it with:
```bash
npx @modelcontextprotocol/inspector
```

Capabilities:
- Connect to any MCP server (stdio or Streamable HTTP)
- Real-time communication logs
- Protocol compliance validation
- Test tool calls interactively with custom inputs
- Inspect resource content and URI templates
- Test prompt rendering
- View transport-level JSON-RPC messages

**Development workflow:** Always test with Inspector before wiring to a real Claude client. Catch schema errors, missing capabilities, and error handling issues before they surface in production.

### VS Code MCP Inspector Extension
Available in VS Code Marketplace (WSO2). Integrated debugging within the IDE — set breakpoints while watching MCP calls.

### Automated Testing Framework

**Five gates for production readiness:**

**Gate 1: Protocol Compliance**
Test that your server correctly implements the MCP specification:
```python
# Using pytest + MCP client library
async def test_initialize():
    client = await create_test_client(server_command)
    result = await client.initialize()
    assert result.protocolVersion in SUPPORTED_VERSIONS
    assert "tools" in result.capabilities

async def test_tools_list():
    tools = await client.list_tools()
    for tool in tools:
        assert len(tool.name) <= 64
        assert tool.inputSchema["type"] == "object"
```

**Gate 2: Tool Correctness**
Unit test each tool's behavior:
```python
async def test_github_create_issue():
    with mock_github_api():
        result = await client.call_tool("github_create_issue", {
            "owner": "test",
            "repo": "test",
            "title": "Test Issue"
        })
        assert not result.isError
        issue = json.loads(result.content[0].text)
        assert "number" in issue
        assert "url" in issue
```

**Gate 3: Error Handling**
Test failure modes explicitly:
```python
async def test_invalid_input():
    result = await client.call_tool("github_create_issue", {
        "owner": "test"
        # missing required 'repo' and 'title'
    })
    assert result.isError
    assert "repo" in result.content[0].text  # actionable error message

async def test_auth_failure():
    with invalid_credentials():
        result = await client.call_tool("github_list_repos", {})
        assert result.isError
        assert "authentication" in result.content[0].text.lower()
```

**Gate 4: Performance**
```python
async def test_tool_latency():
    start = time.time()
    result = await client.call_tool("search_database", {"query": "test"})
    elapsed = time.time() - start
    assert elapsed < 5.0  # 5 second max
    
async def test_pagination():
    result = await client.call_tool("list_issues", {"limit": 10})
    data = json.loads(result.content[0].text)
    assert len(data["items"]) <= 10
    assert "has_more" in data
```

**Gate 5: Security**
```python
async def test_path_traversal():
    result = await client.call_tool("read_file", {
        "path": "../../etc/passwd"
    })
    assert result.isError

async def test_sql_injection():
    result = await client.call_tool("query_database", {
        "query": "'; DROP TABLE users; --"
    })
    assert result.isError
```

---

## Monitoring & Observability

### Key Metrics to Track

**Latency Metrics:**
- `mcp.tool.call.duration_ms` — per-tool, per-status (success/error)
- `mcp.tool.call.p50`, `p95`, `p99` — latency percentiles
- `mcp.session.duration_ms` — session lifetime

**Error Metrics:**
- `mcp.tool.call.error_rate` — per tool, error type
- `mcp.auth.failure_count` — authorization failures (often invisible without explicit tracking)
- `mcp.protocol.error_count` — JSON-RPC level errors

**Usage Metrics:**
- `mcp.tool.call.count` — per tool, per user, per session
- `mcp.session.active_count` — concurrent sessions
- `mcp.tool.list.count` — how often clients enumerate tools

**Business Metrics:**
- Tool adoption rate (which tools get called)
- Task completion rate (tools that returned errors / total calls)
- Session depth (how many tool calls per session)

### Structured Logging Pattern
```json
{
  "level": "info",
  "timestamp": "2025-11-25T10:30:00.123Z",
  "event": "tool_call",
  "server": "github-mcp",
  "session_id": "sess_abc123",
  "tool_name": "github_create_issue",
  "user_id": "user_456",
  "duration_ms": 234,
  "status": "success",
  "input_size_bytes": 128,
  "output_size_bytes": 512,
  "trace_id": "trace_789"
}
```

### OpenTelemetry Integration
For distributed tracing across multi-server MCP deployments:
```python
from opentelemetry import trace

tracer = trace.get_tracer("mcp-server")

@mcp.tool()
async def github_create_issue(owner: str, repo: str, title: str) -> str:
    with tracer.start_as_current_span("github_create_issue") as span:
        span.set_attribute("github.owner", owner)
        span.set_attribute("github.repo", repo)
        # ... implementation
```

**Note:** The `logging` capability in MCP (for sending log messages from server to client) is deprecated in the 2026-07-28 RC. Use `stderr` or OpenTelemetry instead.

### Alert Thresholds
- Error rate > 5% for any tool → P2 alert
- P99 latency > 30 seconds → P2 alert
- Auth failure count > 10/minute → P1 alert (potential attack)
- Session count drops to 0 (server crash) → P1 alert
- Tool definitions changed without deployment → P1 alert (potential rug pull)

---

## Rate Limiting

### Server-Side Rate Limiting
Implement at the tool execution level, not just HTTP level:
```python
from collections import defaultdict
import time

class TokenBucket:
    def __init__(self, rate: float, capacity: float):
        self.rate = rate
        self.capacity = capacity
        self.tokens = defaultdict(lambda: capacity)
        self.last_update = defaultdict(time.time)
    
    def consume(self, user_id: str, cost: float = 1.0) -> bool:
        now = time.time()
        elapsed = now - self.last_update[user_id]
        self.tokens[user_id] = min(
            self.capacity,
            self.tokens[user_id] + elapsed * self.rate
        )
        self.last_update[user_id] = now
        
        if self.tokens[user_id] >= cost:
            self.tokens[user_id] -= cost
            return True
        return False

# Rate limits by tool category
limiter = TokenBucket(rate=10/60, capacity=100)  # 10 calls/minute, burst 100

@mcp.tool()
async def github_create_issue(owner: str, repo: str, title: str) -> str:
    if not limiter.consume(session.user_id, cost=2.0):  # writes cost more
        return MCPError("Rate limit exceeded. Please wait before making more requests.")
```

### Per-Tool Cost Modeling
Different tools have different cost profiles:
- **Read tools**: Low cost, high rate limit (100/minute)
- **Write tools**: Medium cost, medium rate limit (20/minute)
- **Destructive tools**: High cost, low rate limit (5/minute), require confirmation
- **Expensive external API calls** (e.g., AI inference): Model the actual API cost, limit accordingly

### Communicating Rate Limits to the Model
When rate-limited, return an actionable error with timing information:
```python
return {
    "isError": True,
    "content": [{ "type": "text", "text": 
        "Rate limit exceeded for github_create_issue. "
        "Limit: 20 calls/minute. "
        "You can retry in approximately 45 seconds, "
        "or consider batching multiple issue creations."
    }]
}
```

---

## Error Handling Patterns

### The Three-Layer Error Model

**Layer 1 — Protocol errors**: JSON-RPC level (invalid request, method not found). Handled by the framework.

**Layer 2 — Tool execution errors**: Business logic failures. Use `isError: true` in tool response, NOT JSON-RPC error codes. The model needs to read and understand the error to recover.

**Layer 3 — External service errors**: Upstream API failures. Translate to actionable MCP error messages.

```python
try:
    result = await github_api.create_issue(owner, repo, title)
    return format_success(result)
except GitHubRateLimitError as e:
    return mcp_error(f"GitHub API rate limited. Retry after {e.reset_time}. "
                     f"Consider using list_issues to check for duplicates before creating.")
except GitHubAuthError:
    return mcp_error("GitHub authentication failed. Check that GITHUB_TOKEN "
                     "is set and has 'repo' scope.")
except GitHubNotFoundError:
    return mcp_error(f"Repository {owner}/{repo} not found. "
                     f"Use search_repositories to verify the repo exists.")
```

### Graceful Degradation
Design tools to degrade gracefully when external services are unavailable:
```python
@mcp.tool()
async def search_with_fallback(query: str) -> str:
    try:
        return await primary_search(query)
    except ServiceUnavailable:
        try:
            return await cached_search(query)  # return stale cache
        except CacheEmpty:
            return mcp_error("Search service temporarily unavailable. "
                             "Use list_recent_items() to see recently accessed data.")
```

---

## CI/CD for MCP Servers

### Recommended Pipeline
```yaml
# .github/workflows/mcp-server.yml
stages:
  - lint: ruff check / eslint
  - type-check: pyright / tsc --noEmit
  - unit-tests: pytest / jest
  - security-scan: pip-audit / npm audit / bandit
  - integration-tests: test against real API with test credentials
  - mcp-compliance: run MCP Inspector protocol validation
  - build: docker build
  - deploy-staging: deploy, run E2E tests
  - deploy-production: blue/green deploy

# On tool definition changes:
  - require-review: alert team that tool definitions changed, need security re-audit
  - update-manifest-hash: update the approved tool definition hash
```

### Version Management
- **Semantic versioning** for your server: `major.minor.patch`
- Major version bump = breaking changes to tool interfaces or removed tools
- Minor version bump = new tools added
- Patch = bug fixes, security patches
- Communicate breaking changes in advance; give clients 30+ days to migrate
