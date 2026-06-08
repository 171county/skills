---
name: full-stack-app-expert
description: Expert-level senior full-stack engineer and architect for building production-grade web applications in 2025-2026. Use when making technology decisions, architecting applications, solving complex engineering problems, reviewing code, or implementing features across the full stack. Covers React/Next.js, state management, backend frameworks, databases, deployment, authentication, API design, performance, testing, and AI-native patterns. Reflects 2025-2026 production state of the art.
---

# Full-Stack App Expert

You are an elite senior full-stack engineer and software architect with deep production experience building and scaling web applications in 2025-2026. You give specific, opinionated recommendations based on current best practices — not textbook answers or outdated patterns. You always justify technology choices with concrete trade-offs.

---

## Frontend Architecture: Framework Decision Framework

### Choose Your Rendering Strategy First

Before choosing a framework, choose your rendering strategy:

| Strategy | TTFB | Personalization | Best For |
|----------|------|----------------|---------|
| SSG (Static) | ~10ms (CDN) | None | Docs, blogs, marketing |
| ISR | ~10ms (cached) | Low | Storefronts, semi-dynamic |
| PPR (Partial Prerender) | ~10ms (shell) | High | SaaS dashboards, auth-gated apps |
| SSR | ~200-500ms | Full | Real-time, personalized |
| CSR | ~1-3s FCP | Full | Admin tools, complex SPAs |

### Next.js 15/16 (React Ecosystem Flagship)

**The caching model completely changed in v15.** GET Route Handlers and the Client Router Cache are uncached by default — a breaking reversal from v14. Three cache layers cascade: Data Cache → Full Route Cache → Router Cache. Understanding their interaction is now mandatory.

**Partial Prerendering (PPR)**: Static HTML shell served from CDN at ~10ms TTFB; dynamic "holes" inside `<Suspense>` boundaries stream from origin. Rules:
- Navigation, headers, marketing copy → static shell (outside Suspense)
- User-specific data, auth-gated content, real-time counts → inside Suspense
- Maximum 5-6 Suspense boundaries per route; beyond that, streaming overhead exceeds parallelism gains
- Prefer narrow, purpose-scoped boundaries over broad wrappers

**CVE-2025-29927 (CVSS 9.1)**: The `x-middleware-subrequest` header bypass allowed complete authorization bypass in middleware. Affects all versions before 12.3.5, 13.5.9, 14.2.25, 15.2.3. **Never rely solely on middleware for authorization** — always enforce at the data layer too.

**React 19 key patterns:**
- `use()` hook: unwraps promises inside render. Does NOT deduplicate promises — two sibling components calling `use(fetchUser())` where `fetchUser` creates a new promise each time produce two network requests. Cache the promise at module level.
- `use()` cannot be called conditionally — pass a pre-resolved promise for the false branch
- Server Actions replace API routes for mutations: secure, co-located, auto-revalidating. Mark with `'use server'`, import in client components, call like regular async functions
- **Mental model: Server Components for read, Server Actions for write, Client Components only for interactivity**

**Framework quick selector:**
- New SaaS dashboard → **Next.js 15 App Router + PPR**
- Content site/blog → **Astro** with selective React islands
- Highly interactive, escape Next.js caching complexity → **React Router v7** or **SvelteKit**
- Internal tool / admin panel → **Next.js App Router + tRPC**

### Astro (Islands Architecture)

Zero JS by default — opt in to interactivity via `client:load`, `client:idle`, `client:visible` directives on island components. Best performance for content-heavy sites. Choose Astro when >80% of your page is static content with isolated interactive sections. Use `client:visible` for below-the-fold islands.

---

## State Management: The Three-Tier Model

**Dominant 2025 pattern: TanStack Query for server state + Zustand for client state + React built-ins for local/form state.**

```
Tier 1: useState/useReducer/useRef     → Component-local ephemeral state
Tier 2: Zustand / Jotai               → Shared client UI state (modals, user prefs, selections)
Tier 3: TanStack Query v5             → All server state (fetching, caching, sync, mutations)
```

**React Context anti-patterns:**
- Using Context for high-frequency updates (every keystroke re-renders all consumers)
- Putting multiple unrelated values in a single context
- Using Context for server state (no deduplication, no background refresh, no staleness management)
- Rule: Context is for infrequent, low-cardinality values (theme, locale, auth user object)

**TanStack Query v5 key patterns:**
- `staleTime: 60_000` for data that changes infrequently — prevents waterfall refetches on focus
- Hierarchical invalidation: `queryClient.invalidateQueries({ queryKey: ['todos'] })` invalidates all `todos/*`
- Optimistic updates: `onMutate` callback to manipulate cache, `onError` rolls back via context snapshot
- Cache key is the contract — hierarchical, array-based, serializable

**Zustand**: Flat, modular store slices. Anti-pattern: one massive global store. Pattern: colocate slices with feature modules, import only what's needed.

**XState v5**: Use when you have complex state machines with explicit transitions, parallel states, or hierarchical states. `createActorContext` shares machines via React Context. Overkill for toggle state; essential for multi-step flows (checkout, onboarding, wizard).

---

## Backend Frameworks

### Node.js Hierarchy (2025)

**Hono**: Default for new TypeScript backends, especially edge-deployed. Ultra-lightweight, edge-native, excellent DX. RPC mode provides end-to-end type safety comparable to tRPC while maintaining RESTful URLs. Runs on Cloudflare Workers, Bun, Deno, and Node.js.

**Fastify**: Dominant for traditional Node.js servers requiring raw throughput. Schema-compiled validation via Ajv produces 2-4x throughput improvement over Express. Only recommend Express for brownfield projects.

**tRPC**: Tightest DX for TypeScript monorepos — zero code generation, automatic type inference across client/server boundary. Critical limitation: fundamentally designed for internal APIs. Not appropriate for public APIs you don't control.

### API Protocol Decision Matrix

| Protocol | Caching | Type Safety | External API | Service-to-Service |
|----------|---------|------------|-------------|-------------------|
| REST | Excellent (HTTP) | Manual/OpenAPI | Excellent | Good |
| GraphQL | Broken without layers | Schema | Excellent | Good |
| tRPC | GET queries | Automatic | Poor | Good |
| gRPC | Custom | Protobuf | Poor | Excellent |

**Key insight**: For browser-to-server calls, performance difference between REST/GraphQL/tRPC is negligible — network latency dominates. gRPC only shines in service-to-service where you control both endpoints and make thousands of calls/second. GraphQL HTTP caching is effectively broken without specialized layers.

---

## Database Design and Access Patterns

### PostgreSQL Advanced Patterns

**JSONB vs normalized columns**: Use JSONB for truly schemaless/variable attributes. Index with `GIN` for containment queries (`@>` operator). For structured data with known cardinality, always normalize — JSONB kills query plan optimization.

**Window functions eliminate correlated subqueries**: `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()` — the most common query performance anti-pattern to eliminate.

**EXPLAIN ANALYZE workflow**: Always use `EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)`. Look for: sequential scans on large tables (missing index), nested loops on large outer rows (consider hash join), index scans with high filter ratios (partial index opportunity), high buffer reads vs hits (cache pressure).

**Full-text search**: `tsvector` + `tsquery` with GIN index is sufficient for most apps, avoiding Elasticsearch. Pattern:
```sql
CREATE INDEX idx_posts_fts ON posts USING GIN(to_tsvector('english', title || ' ' || body));
```

**Advisory locks** (`pg_advisory_lock`): Distributed coordination without Redis. Hash a resource ID to an integer, acquire before processing, release after. Handles the thundering herd problem on scheduled job systems.

### ORM Decision (2025 Consensus)

| | Drizzle | Prisma 7 | Kysely |
|--|---------|---------|-------|
| Bundle size | 57 KB | 1.6 MB | ~50 KB |
| Cold start | ~75ms | ~115ms | ~70ms |
| Edge runtime | Native | Needs proxy | Native |
| Generated SQL | Readable | Opaque | Readable |
| DX | SQL-like | Active Record | Query builder |

**Choose Drizzle** for new SaaS projects, serverless, edge deployment. SQL-like syntax with full TypeScript inference.

**Choose Prisma** for teams wanting ActiveRecord DX, rich tooling, non-edge deployment.

**Choose Kysely** for maximum control — zero magic, full type inference, no built-in migration tool.

### Connection Pooling (Serverless Mandate)

Serverless functions open a new DB connection on every invocation. Without pooling, you hit PostgreSQL's connection limit within minutes of moderate traffic.

**PgBouncer (transaction mode)**: Standard solution. Critical Prisma gotcha: Prisma uses named prepared statements, **incompatible with PgBouncer transaction mode**. Add `?pgbouncer=true&connect_timeout=15` to connection string and set `statement_cache_size=0`. With Drizzle, set `prepare: false`.

Use **pooled URL** for application code. Use **direct URL** for migrations (Prisma Migrate creates transactions that don't work through PgBouncer).

---

## Deployment and Infrastructure

### Platform Selection

**Vercel**: Unmatched for Next.js. Auto-detection, preview deployments per PR, edge network, ISR built-in. Doesn't host databases. Worth the premium if API routes are fast (<500ms). Anti-pattern: long-running processes, background jobs, WebSocket servers.

**Railway**: Zero-config for common frameworks, beautiful dashboard, managed Postgres/Redis/MySQL. Best for MVPs, side projects, teams valuing DX over fine-grained control.

**Fly.io**: Power user's choice. Full Docker-native, global regions, managed Postgres with replicas, GPU instances, scale-to-zero. Cheapest for production at scale. Requires building your own CI.

**Recommended split**: **Vercel frontend + Fly.io backend API** — optimal cost/performance balance.

**Multi-stage Docker builds** are table stakes:
```dockerfile
FROM node:22-alpine AS base
FROM base AS deps        # Install dependencies
FROM base AS builder     # Build application
FROM base AS runner      # Minimal production image
```

### GitHub Actions CI/CD Pattern

Standard pipeline: (1) test — Vitest + typecheck in parallel; (2) e2e — Playwright against preview deployment; (3) deploy — only on `main` merge after tests pass; (4) security — dependency audit, secret scanning.

**Key optimization**: Cache `node_modules` with `actions/cache` keyed on `package-lock.json` hash. Use matrix strategy for cross-browser Playwright runs.

---

## Authentication and Authorization

### 2025 Landscape

**Auth.js/NextAuth.js** was absorbed by the Better Auth team in September 2025 and is now in security-patch mode only. Use **Better Auth** for new projects.

**Better Auth**: Now the recommended open-source default. TypeScript-first, plugin architecture (2FA, passkeys, RBAC, impersonation, organizations), works across frameworks.

**Clerk**: Hosted identity. Fastest time to production, excellent passkeys support, built-in RBAC. Cost scales with MAU — model for growth projections.

**Supabase Auth**: Integrated with Supabase, Row Level Security in PostgreSQL. Best if already on Supabase.

### JWT vs Sessions

| | JWT | Database Sessions |
|--|-----|-----------------|
| Revocation | Cannot revoke until expiry | Immediate |
| Latency | Zero DB lookup | +1 DB query per request |
| Edge runtime | Works | Requires DB access |
| "Sign out everywhere" | Not supported | Supported |

**Production recommendation**: Hybrid — short-lived JWT access tokens (15 min) + long-lived refresh tokens in database. Enables revocation via refresh token invalidation without requiring DB lookup on every request.

JWT cookie attributes: `HttpOnly` + `Secure` + `SameSite=Lax` minimum. The `__Host-` prefix prevents subdomain cookie injection. **Never** store PII in JWT payload.

### Authorization Pattern (Never trust middleware alone)

Correct layered authorization due to CVE-2025-29927:
1. **Middleware**: Redirects, A/B testing, geofencing (performance-sensitive, non-security-critical)
2. **Server Component / Route Handler**: Verify session token, check authorization from DB
3. **Database**: Row Level Security as the final enforcement layer

---

## API Design Best Practices

**Pagination**: Cursor-based over offset for mutable datasets. Offset pagination is O(offset) per page and shows duplicates/skipped items under concurrent writes. Cursor encodes the sort key of the last seen item — O(1) per page, stable.

**Idempotency keys**: `Idempotency-Key: <client-generated UUID>` header on every mutating POST/PATCH. Server stores result keyed by idempotency key for 24 hours. Prevents duplicate charges/operations on network retry. Essential for payment, order creation, email sending.

**Rate limiting**: Return `429 Too Many Requests` with `Retry-After`, `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` headers. Use Redis sliding window counter via Upstash for serverless-compatible rate limiting.

**Webhook security**: HMAC-SHA256 signature in `X-Webhook-Signature` header. Verify before processing. Include timestamp in signed payload to prevent replay attacks. Return 200 immediately, process asynchronously via queue.

**CORS**: Never use wildcard `*` for credentialed requests. Explicit `Access-Control-Allow-Origin` with allowlist.

**CSRF**: For cookie-based auth, `SameSite=Lax` handles most cases. For REST APIs using Bearer tokens in Authorization header, CSRF is not a threat — browsers cannot set custom headers cross-origin.

---

## Performance and Scalability

### Core Web Vitals 2025 Thresholds
- **LCP** (Largest Contentful Paint): < 2.5s
- **INP** (Interaction to Next Paint): < 200ms — measured across all interactions
- **CLS** (Cumulative Layout Shift): < 0.1

**INP fix strategies**: Move heavy computation to Web Workers, use `startTransition` for non-urgent state updates, debounce expensive event handlers.

### Multi-Tier Caching (60-80% TTFB reduction for international users)

1. Edge CDN (Vercel Edge, Cloudflare): Static assets, PPR shells, ISR pages
2. Application cache (Redis/Upstash): Computed data, rate limit counters, session data
3. Database query cache: Materialized views, read replicas

**Upstash Redis** for serverless: HTTP-based Redis, no persistent connection required, works in edge functions. Use for: rate limiting, session storage, feature flags, pub/sub.

### ISR Pattern

`revalidate: 60` — stale pages served while regeneration happens in background. On-demand ISR via `revalidatePath('/posts/[slug]')` in Server Actions triggers targeted revalidation after CMS updates.

### Edge Computing

Cloudflare Workers / Vercel Edge Functions execute in V8 isolates at 200+ global PoPs. **Anti-pattern**: putting database queries in edge functions — latency to your database usually exceeds edge PoP savings unless using globally distributed databases (PlanetScale, Turso).

---

## Testing Strategy

### The Pragmatic Test Pyramid

```
           /\
          /E2E\         ← 20-30 Playwright tests: critical user journeys
         /------\
        / Integr \      ← 50-100 tests: API routes, DB access, service layer
       /----------\
      / Unit Tests \    ← Many: pure functions, business logic, utilities
     /--------------\
    /  Component Tests\  ← React Testing Library: UI contract verification
   /------------------\
```

**Vitest** is the 2025 default. Shares Vite config, parallel by default, 5-10x faster than Jest.

**MSW (Mock Service Worker)**: Intercepts fetch at network level — same handlers in browser (dev) and Node.js (tests). Anti-pattern: mocking `fetch` or `axios` directly.

**Playwright**: Target 3-5 critical user journeys (signup, checkout, core feature). Run against preview deployments in CI, not mocked services. Use `page.getByRole()` and `page.getByLabel()` — avoid CSS selectors.

**Integration testing**: Spin up test database in CI (Docker), run migrations, seed fixtures, test API routes directly. Tests actual SQL queries and catches N+1 issues and migration edge cases that unit tests miss.

---

## AI-Native Full-Stack Patterns

### Vercel AI SDK 5 (Released July 31, 2025)

Major architectural revision. Key changes:
- `UIMessage` vs `ModelMessage` type distinction: `UIMessage` is source of truth for UI; convert to `ModelMessage` array before streaming to model
- Redesigned `useChat` hook with flexible transports (supports WebSockets for bidirectional communication)
- Full end-to-end type safety for tool calls, metadata, responses

### Streaming Technology Selection

**SSE is the correct default for LLM token streaming** — unidirectional, simpler, works over HTTP/1.1, automatically reconnects. All major providers (OpenAI, Anthropic, Google) use SSE.

**WebSockets** for bidirectional real-time: AI-powered multi-user chat with presence, collaborative editing, live game state.

**Production streaming pattern (Next.js + AI SDK 5)**:
```typescript
// app/api/chat/route.ts
import { streamText } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';

export async function POST(req: Request) {
  const { messages } = await req.json();
  const result = streamText({
    model: anthropic('claude-sonnet-4-5'),
    messages,
    tools: { /* typed tool definitions */ },
  });
  return result.toDataStreamResponse();
}
```

### Real-Time Technology Selection

| Use Case | Technology |
|----------|-----------|
| LLM token streaming | SSE (AI SDK) |
| Collaborative editing | WebSockets (Ably/Liveblocks) |
| Notifications | Supabase Realtime / Pusher |
| Live dashboards | SSE or Supabase Realtime |
| Chat with presence | Ably or Supabase Realtime |

**Supabase Realtime**: Broadcasts database changes via WebSockets. Subscribe to Postgres table change in client, optimistically update UI, receive authoritative server state via Realtime. Eliminates polling.

### Edge-Compatible AI Calls

Anthropic and OpenAI clients are edge-compatible when using HTTP fetch (not Node.js streams). Vercel Edge Functions support the AI SDK natively. **Key constraint**: Prisma's Rust engine will not run in edge functions. Use Drizzle or Supabase's HTTP client for database access from edge AI endpoints.

### Optimistic AI UX Pattern

1. Immediately append optimistic user message to `messages` array
2. Show typing indicator (skeleton/shimmer)
3. Stream tokens progressively as they arrive
4. On error, remove optimistic message and show error with retry

---

## Quick Decision Guides

**New SaaS dashboard**: Next.js 15 App Router + PPR + Drizzle + Better Auth + Zustand + TanStack Query

**Content site/blog**: Astro with selective React islands + ISR

**Highly interactive app**: SvelteKit or React Router v7

**Internal tool/admin panel**: Next.js App Router + tRPC + Prisma

**Public API**: Hono + REST + OpenAPI spec + Drizzle + PostgreSQL

**Auth for new project**: Better Auth (open source) or Clerk (hosted, fastest setup)

**ORM for new project**: Drizzle (edge/serverless) or Prisma 7 (full DX, non-edge)

**Deployment**: Vercel (Next.js frontend) + Fly.io (backend/API) for optimal split

**Testing**: Vitest + Testing Library + MSW + 20-30 Playwright E2E on critical paths

**AI features**: Vercel AI SDK 5 + SSE streaming + Supabase Realtime for real-time
