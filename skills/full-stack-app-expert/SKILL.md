---
name: full-stack-app-expert
description: Expert-level full-stack application developer covering React 19 (Compiler, Server Components, Actions), Next.js 15 (caching breaking changes, PPR, Turbopack), Vite 8, Astro, SvelteKit, TypeScript best practices, CSS and component design systems (Radix UI, Shadcn/UI, Tailwind CSS), state management, backend frameworks (Node.js, FastAPI, Django, Go), databases (PostgreSQL, MySQL, SQLite, MongoDB), ORM layer (Prisma vs Drizzle), API design (REST, GraphQL, tRPC), authentication and authorization, deployment platforms (Vercel, Cloudflare, Fly.io, Railway), CI/CD, monorepo tooling (Turborepo, Nx), observability, cloud-native patterns, security hardening, performance optimization, AI integration in full-stack apps, and environment management. Expert-level reference current as of June 2026.
---

# Full Stack App Expert

## Overview and Expert Mandate

Full-stack development in 2025-2026 has undergone fundamental shifts: React Server Components and Server Actions have changed how data flows from server to client; Next.js 15's caching defaults changed (breaking from v14); Turbopack is now the default development server; AI integration is now a standard full-stack concern; and the edge runtime has matured into a production pattern. This skill provides expert-level guidance on every layer of the modern full-stack application — from UI component architecture through database patterns, deployment, and observability.

---

## 1. Modern Frontend Frameworks

### React 19

React 19 (stable December 2024; Compiler v1.0 October 2025) is the most consequential release since hooks.

#### React Compiler

The React Compiler is a **build-time transformation** that automatically inserts memoization (equivalent of `useMemo`/`useCallback`/`React.memo`) where safe to do so.

**Key nuance:** The compiler *reduces* — but does NOT *eliminate* — the need for manual memoization. You still need manual memoization at:
- Third-party library interop boundaries
- Cases requiring precise referential identity control
- Components the compiler cannot statically analyze

**Practical guidance:** Write components naturally first. Profile before adding manual memoization. Treat the compiler as default; manual memoization as last resort.

#### New Hooks and APIs

| Hook/API | Purpose | Key Behavior |
|----------|---------|-------------|
| `use(promise)` | Read async resource in render | Callable conditionally; integrates with Suspense |
| `use(Context)` | Read Context value | Replaces `useContext`; can be conditional |
| `useActionState(fn, initial)` | Manage async action state | Returns `[state, dispatch, isPending]` |
| `useFormStatus()` | Read parent form state | Must be inside `<form>`; returns `{pending, data, method, action}` |
| `useOptimistic(state, fn)` | Optimistic UI | Automatically reverts on failure |

#### React Server Components (RSC)

Execute on server only. Zero JavaScript ships to client for RSC components.

**Characteristics:**
- Cannot use hooks, browser APIs, or event handlers
- Can `async/await` directly — no `useEffect + useState` fetch pattern needed
- Compose with Client Components via `"use client"` boundary
- SSR streaming reduces TTFB by average ~340ms on first contentful paint

**Mental model:** All components in Next.js App Router are Server Components by default. Add `"use client"` only when you need interactivity, browser APIs, or hooks.

#### Server Actions

```typescript
// Server Action — runs only on server
async function createPost(formData: FormData) {
  "use server";
  const title = formData.get("title") as string;
  await db.post.create({ data: { title } });
  revalidatePath("/posts");
}

// In Server Component — no JS needed for basic form submit
export default function Page() {
  return (
    <form action={createPost}>
      <input name="title" required />
      <button type="submit">Create</button>
    </form>
  );
}
```

### Next.js 15

#### Critical Caching Change (Breaking from v14)

| Behavior | Next.js 14 default | Next.js 15 default |
|----------|-------------------|-------------------|
| `fetch()` in Server Components | `force-cache` | `no-store` |
| GET Route Handlers | cached statically | dynamic (uncached) |
| Client Router Cache | cached indefinitely | not cached for dynamic pages |

**Migration rule:** When upgrading from 14 to 15, explicitly add `cache: 'force-cache'` to fetch calls that should be cached, or your app will silently over-fetch.

#### Turbopack Dev (Stable in Next.js 15)

Turbopack is the default for `next dev`. Substantially faster HMR and server startup. `next build` still uses Webpack by default (Turbopack builds in beta).

#### Partial Pre-Rendering (PPR)

Combine static shell delivery (instant from CDN) with dynamic streaming content:

```typescript
// next.config.ts
export default {
  experimental: { ppr: 'incremental' }
}

// Route segment opts in
export const experimental_ppr = true;

// Static shell renders at build time
// Dynamic Suspense boundaries stream from server
```

#### App Router Best Practices

```
app/
├── (marketing)/          # Route group — no URL segment
│   ├── page.tsx          # Server Component by default
│   └── layout.tsx
├── (app)/
│   ├── dashboard/
│   │   ├── page.tsx      # async Server Component
│   │   ├── loading.tsx   # Suspense fallback
│   │   └── error.tsx     # Error boundary
│   └── layout.tsx
├── api/
│   └── webhooks/
│       └── route.ts
└── layout.tsx
```

Key patterns:
- Co-locate data fetching with the component that needs it
- Use `generateStaticParams` for static dynamic routes
- Use `unstable_cache` (stable in 15) for fine-grained server-side caching

### Astro (Islands Architecture)

Default output: zero JavaScript — pure HTML/CSS. Interactive "islands" hydrate independently:

```astro
---
// Server-only code here
import ReactCounter from '../components/Counter.jsx';
---

<html>
  <body>
    <h1>Static content</h1>
    <!-- Island hydrates only when visible -->
    <ReactCounter client:visible />
  </body>
</html>
```

`client:load` (immediately) | `client:idle` (when browser idle) | `client:visible` (when in viewport) | `client:only` (CSR only, no SSR)

Ideal for: marketing sites, blogs, documentation, e-commerce product pages.

### SvelteKit

Svelte 5 Runes replace `$:` reactive declarations:

```svelte
<script>
  let count = $state(0);
  let doubled = $derived(count * 2);
  
  $effect(() => {
    console.log(`Count changed to ${count}`);
  });
</script>

<button onclick={() => count++}>
  Count: {count} (doubled: {doubled})
</button>
```

- 30-50% smaller JS bundles than equivalent React apps
- Universal rendering per-route via `export const prerender`, `export const ssr`
- Adapters for Vercel, Netlify, Cloudflare, Node, static

---

## 2. TypeScript Best Practices

### Type System Fundamentals

```typescript
// Prefer union types over enums
type Status = "pending" | "active" | "inactive"; // ✓
enum Status { Pending, Active, Inactive }          // ✗ (generates runtime code)

// Use const assertions for object literals
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000
} as const; // all properties become readonly literals

// Utility types
type Partial<T>        // All properties optional
type Required<T>       // All properties required
type Readonly<T>       // All properties readonly
type Pick<T, K>        // Select subset of properties
type Omit<T, K>        // Exclude subset of properties
type Record<K, V>      // Dictionary type
type NonNullable<T>    // Remove null/undefined
type ReturnType<T>     // Extract function return type
type Parameters<T>     // Extract function parameter types

// Conditional types
type IsArray<T> = T extends unknown[] ? true : false;

// Template literal types
type EventName = `on${Capitalize<string>}`;
```

### Strict Mode and Type Safety

```json
// tsconfig.json — strict configuration
{
  "compilerOptions": {
    "strict": true,                // Enables all strict checks
    "noUncheckedIndexedAccess": true,  // Array[i] returns T | undefined
    "exactOptionalPropertyTypes": true, // undefined !== missing property
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "verbatimModuleSyntax": true   // Explicit type imports
  }
}
```

### Zod for Runtime Validation

```typescript
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  role: z.enum(["admin", "user", "viewer"]),
  createdAt: z.date(),
  metadata: z.record(z.string(), z.unknown()).optional()
});

type User = z.infer<typeof UserSchema>;  // Derive TypeScript type from schema

// Validate at API boundaries
function createUser(input: unknown): User {
  return UserSchema.parse(input);  // Throws ZodError if invalid
}

// Safe parse (no throw)
const result = UserSchema.safeParse(input);
if (result.success) {
  console.log(result.data);  // Typed User
} else {
  console.error(result.error.flatten());
}
```

---

## 3. CSS and Component Design Systems

### Tailwind CSS v4 (2025)

```typescript
// tailwind.config.ts — v4 uses CSS-first configuration
// @import "tailwindcss" in your CSS file instead of JS config

// Component with variants
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
        ghost: "hover:bg-accent hover:text-accent-foreground",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: { variant: "default", size: "default" }
  }
);

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>,
  VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return <button className={buttonVariants({ variant, size, className })} {...props} />;
}
```

### Radix UI and Shadcn/UI

**Radix UI:** Unstyled, accessible component primitives. Handles ARIA, keyboard navigation, focus management. You style them.

**Shadcn/UI:** Pre-styled components built on Radix. Copy-paste components into your codebase (you own the code).

```bash
# Initialize shadcn in your Next.js project
npx shadcn@latest init
# Add specific components
npx shadcn@latest add button dialog table form
```

---

## 4. State Management

### When to Use Each Approach

| Approach | Use When | 2025 Recommendation |
|----------|---------|---------------------|
| React state (useState/useReducer) | Local component state | Always prefer first |
| Context API | Low-frequency global state (theme, auth) | Fine for infrequent updates |
| Zustand | Complex client state, frequent updates | Best general-purpose 2025 |
| Jotai | Atomic state, fine-grained updates | Good for complex derived state |
| Redux Toolkit | Large teams, need devtools | Only if team already uses it |
| TanStack Query | Server state (cached API data) | Required for server data |
| Valtio | Mutable state with proxy | Good if preferred over reducers |

### TanStack Query (Server State)

```typescript
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

// Fetch with automatic caching, background refetch, stale-while-revalidate
function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then(r => r.json()),
    staleTime: 5 * 60 * 1000,  // 5 minutes before refetch
    gcTime: 10 * 60 * 1000,    // 10 minutes in cache after component unmounts
  });
}

// Mutation with optimistic update
function useCreateUser() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: (data: CreateUserInput) =>
      fetch("/api/users", { method: "POST", body: JSON.stringify(data) }).then(r => r.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });
}
```

---

## 5. Backend Frameworks

### Node.js/TypeScript Backends

**Hono (2025 leading choice for edge + Node.js):**
```typescript
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { z } from "zod";

const app = new Hono();

app.get("/health", (c) => c.json({ status: "ok" }));

app.post(
  "/users",
  zValidator("json", z.object({
    email: z.string().email(),
    name: z.string().min(1)
  })),
  async (c) => {
    const { email, name } = c.req.valid("json");
    const user = await createUser({ email, name });
    return c.json(user, 201);
  }
);

export default app;  // Works on Cloudflare Workers, Bun, Node.js
```

**Express.js:** Still dominant for large teams; vast ecosystem; not edge-compatible.

**Fastify:** 2x faster than Express; excellent TypeScript support; plugin ecosystem.

### Python Backends

**FastAPI (recommended):**
```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel

app = FastAPI()

class UserCreate(BaseModel):
    email: str
    name: str

@app.post("/users", status_code=201)
async def create_user(user: UserCreate, db: AsyncSession = Depends(get_db)):
    existing = await db.execute(select(User).where(User.email == user.email))
    if existing.scalar_one_or_none():
        raise HTTPException(status_code=409, detail="Email already registered")
    
    new_user = User(email=user.email, name=user.name)
    db.add(new_user)
    await db.commit()
    return {"id": new_user.id, "email": new_user.email}
```

### Go for High-Performance Services

Go excels for: High-concurrency services, CLI tools, systems programming, microservices with strict latency requirements.

```go
package main

import (
    "encoding/json"
    "net/http"
    "github.com/go-chi/chi/v5"
)

type Handler struct {
    db *sql.DB
}

func (h *Handler) GetUser(w http.ResponseWriter, r *http.Request) {
    id := chi.URLParam(r, "id")
    var user User
    err := h.db.QueryRowContext(r.Context(), 
        "SELECT id, email, name FROM users WHERE id = $1", id).
        Scan(&user.ID, &user.Email, &user.Name)
    if err == sql.ErrNoRows {
        http.Error(w, "Not found", http.StatusNotFound)
        return
    }
    json.NewEncoder(w).Encode(user)
}
```

---

## 6. Databases

### PostgreSQL (Primary Recommendation)

PostgreSQL is the default choice for most full-stack applications:
- ACID transactions
- Rich indexing (B-tree, GiST, GIN, BRIN, partial, expression)
- JSON/JSONB for semi-structured data
- Full-text search (without Elasticsearch for many use cases)
- pgvector extension for AI embeddings
- Row-level security for multi-tenant applications
- Logical replication and point-in-time recovery

**2025 managed options:**
- **Supabase:** Postgres + Auth + Storage + Edge Functions + Realtime; excellent DX
- **Neon:** Serverless Postgres with branching; ideal for Next.js/Vercel deployments
- **PlanetScale:** MySQL-compatible; Git-like schema branching (note: departed from open-source model 2024)
- **Turso:** SQLite-based edge database; per-region data placement

### Schema Design Patterns

```sql
-- Core patterns
CREATE TABLE users (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email       TEXT NOT NULL UNIQUE,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Soft deletes (prefer over hard deletes for most business data)
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMPTZ;
CREATE INDEX idx_users_active ON users (id) WHERE deleted_at IS NULL;

-- Row-level security for multi-tenant
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
CREATE POLICY "users_see_own_posts" ON posts
    FOR SELECT USING (user_id = auth.uid());

-- JSONB for flexible metadata
ALTER TABLE users ADD COLUMN metadata JSONB DEFAULT '{}';
CREATE INDEX idx_users_metadata ON users USING GIN (metadata);
-- Query: SELECT * FROM users WHERE metadata->>'plan' = 'enterprise';
```

### Performance Tuning

```sql
-- EXPLAIN ANALYZE on slow queries
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
SELECT u.*, COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON p.user_id = u.id
WHERE u.created_at > NOW() - INTERVAL '30 days'
GROUP BY u.id;

-- Indexes for common patterns
CREATE INDEX CONCURRENTLY idx_posts_user_created 
    ON posts (user_id, created_at DESC);  -- Covering index for user's recent posts

-- Connection pooling (critical for serverless)
-- Use PgBouncer or Supabase's built-in pooler
-- Set pool_mode = transaction for serverless (vs. session mode)
```

---

## 7. ORM Layer: Prisma vs. Drizzle

### Prisma

```typescript
// schema.prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

// Usage
const users = await prisma.user.findMany({
  where: { 
    createdAt: { gte: new Date(Date.now() - 30 * 24 * 60 * 60 * 1000) }
  },
  include: { _count: { select: { posts: true } } },
  orderBy: { createdAt: "desc" },
  take: 20
});
```

**Prisma strengths:** Excellent DX; auto-generated types; migrations; Prisma Studio GUI; large ecosystem.

**Prisma weaknesses:** Generated SQL can be suboptimal; adds ~200KB to bundle; connection handling overhead in serverless.

### Drizzle ORM

```typescript
// schema.ts
import { pgTable, text, timestamp } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: text("id").primaryKey().$defaultFn(() => crypto.randomUUID()),
  email: text("email").notNull().unique(),
  name: text("name"),
  createdAt: timestamp("created_at").defaultNow().notNull()
});

// Usage — SQL-like, typed
const recentUsers = await db
  .select({ id: users.id, email: users.email, postCount: count(posts.id) })
  .from(users)
  .leftJoin(posts, eq(posts.userId, users.id))
  .where(gte(users.createdAt, new Date(Date.now() - 30 * 24 * 60 * 60 * 1000)))
  .groupBy(users.id)
  .orderBy(desc(users.createdAt))
  .limit(20);
```

**Drizzle strengths:** Thin abstraction; SQL-like syntax; excellent performance; small bundle; edge-compatible; no magic.

**Drizzle weaknesses:** Less ORM features (no Prisma-style nested create); smaller ecosystem; less mature tooling.

**Selection guide:**
- New project, team unfamiliar with SQL: Prisma (better DX, less error-prone)
- Edge/serverless functions requiring small bundle: Drizzle
- Performance-critical or complex queries: Drizzle (more control over generated SQL)
- Existing team with SQL knowledge: Drizzle (more transparent)

---

## 8. API Design

### tRPC (Type-Safe APIs)

```typescript
// server/router.ts
import { initTRPC, TRPCError } from "@trpc/server";
import { z } from "zod";

const t = initTRPC.context<Context>().create();
const protectedProcedure = t.procedure.use(authMiddleware);

export const appRouter = t.router({
  user: t.router({
    getById: protectedProcedure
      .input(z.object({ id: z.string().uuid() }))
      .query(async ({ input, ctx }) => {
        const user = await ctx.db.user.findUnique({ where: { id: input.id } });
        if (!user) throw new TRPCError({ code: "NOT_FOUND" });
        return user;
      }),
    create: protectedProcedure
      .input(z.object({ email: z.string().email(), name: z.string() }))
      .mutation(async ({ input, ctx }) => {
        return ctx.db.user.create({ data: input });
      })
  })
});

// client usage — full type inference, no codegen
const user = await trpc.user.getById.query({ id: "..." });
// user is fully typed as User
```

### REST API Design Principles

```
GET    /users              # List users (paginated)
POST   /users              # Create user
GET    /users/:id          # Get user
PATCH  /users/:id          # Partial update
DELETE /users/:id          # Soft delete

# Nested resources (max 2 levels)
GET    /users/:id/posts    # User's posts
POST   /users/:id/posts    # Create post for user

# Use query params for filtering/sorting/pagination
GET /users?status=active&sort=createdAt&order=desc&cursor=xxx&limit=20
```

**Response envelope pattern:**
```json
{
  "data": [...],
  "meta": {
    "total": 150,
    "cursor": "eyJpZCI6IjEyMyJ9",
    "hasMore": true
  }
}
```

### OpenAPI Documentation

```typescript
// Using Zod + Hono + @hono/zod-openapi
import { createRoute, z } from "@hono/zod-openapi";

const createUserRoute = createRoute({
  method: "post",
  path: "/users",
  request: {
    body: {
      content: {
        "application/json": {
          schema: CreateUserSchema
        }
      }
    }
  },
  responses: {
    201: {
      content: {
        "application/json": {
          schema: UserSchema
        }
      },
      description: "User created successfully"
    }
  }
});
```

---

## 9. Authentication and Authorization

### Authentication Stack (2025)

**Clerk (SaaS, fastest to ship):**
- Complete auth solution: sign-up/in, MFA, social OAuth, SAML SSO
- React components out of the box
- $25/month for < 10K users; scales with usage
- Best choice when speed to market > cost

**Auth.js (NextAuth v5 — open source):**
```typescript
// auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import Credentials from "next-auth/providers/credentials";

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    GitHub,
    Credentials({
      async authorize(credentials) {
        const user = await verifyCredentials(credentials);
        return user ?? null;
      }
    })
  ],
  callbacks: {
    session({ session, token }) {
      session.user.id = token.sub!;
      return session;
    }
  }
});
```

**Lucia Auth:** Auth library (not full service); handles sessions; you manage storage; maximum control.

### JWT Best Practices

```typescript
import { SignJWT, jwtVerify } from "jose";

const secret = new TextEncoder().encode(process.env.JWT_SECRET);

async function createToken(payload: { userId: string; role: string }) {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: "HS256" })
    .setIssuedAt()
    .setExpirationTime("15m")  // Short-lived access tokens
    .sign(secret);
}

// Refresh token pattern: 15-min access token + 7-day refresh token
// Store refresh tokens in httpOnly cookies; access tokens in memory
```

### Row-Level Security (Multi-tenant)

```sql
-- Enable RLS + policy pattern (Supabase pattern)
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Users can only read their org's documents
CREATE POLICY "org_isolation" ON documents
    FOR ALL USING (
        organization_id = (
            SELECT organization_id FROM users 
            WHERE id = auth.uid()
        )
    );
```

---

## 10. Deployment Platforms

### Platform Comparison (2025-2026)

| Platform | Best For | Strengths | Pricing |
|----------|---------|-----------|---------|
| Vercel | Next.js apps | DX, edge network, zero-config | $20/mo + usage |
| Cloudflare Pages + Workers | Global edge, zero-latency | Free tier generous; Workers edge runtime | $5/mo + usage |
| Fly.io | Full-stack, databases, long-running | True containers, multi-region, fast | $0 + usage |
| Railway | Full-stack + databases | Easy Postgres, Redis, no K8s complexity | $5/mo + usage |
| Render | Simpler Heroku replacement | Auto-scaling, managed DBs | $7/mo + usage |
| AWS/GCP/Azure | Enterprise, custom infra | Maximum flexibility, compliance | Variable |

### Deployment Architecture Patterns

**Vercel (Next.js 15) optimal structure:**
```
vercel.json
{
  "functions": {
    "app/api/**": {
      "maxDuration": 60,    // Route timeout in seconds
      "memory": 1024        // Memory limit in MB
    }
  },
  "regions": ["iad1", "sfo1"]  // Deploy to specific regions
}
```

**Cloudflare Workers edge deployment:**
```typescript
// worker.ts — runs in V8 isolates on 300+ edge locations
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    // Edge-cached response
    const cacheKey = new Request(url.toString(), request);
    const cache = caches.default;
    let response = await cache.match(cacheKey);
    
    if (!response) {
      response = await handleRequest(request, env);
      const cachedResponse = new Response(response.body, response);
      cachedResponse.headers.set("Cache-Control", "public, max-age=60");
      ctx.waitUntil(cache.put(cacheKey, cachedResponse));
    }
    
    return response;
  }
};
```

---

## 11. CI/CD Pipelines and Testing

### Testing Strategy

**Testing pyramid for full-stack apps:**
```
              E2E (Playwright) — 10-20 critical paths
            /
          Integration tests — 30-40% of tests
         /
      Unit tests — 50-60% of tests
```

**Vitest (unit tests):**
```typescript
import { describe, it, expect, vi } from "vitest";
import { createUser } from "./users";

describe("createUser", () => {
  it("hashes the password before storing", async () => {
    const mockDb = { user: { create: vi.fn().mockResolvedValue({ id: "1" }) } };
    await createUser({ email: "test@example.com", password: "plaintext" }, mockDb);
    
    const storedUser = mockDb.user.create.mock.calls[0][0].data;
    expect(storedUser.password).not.toBe("plaintext");
    expect(storedUser.password).toMatch(/^\$2b\$/); // bcrypt hash
  });
});
```

**Playwright (E2E):**
```typescript
import { test, expect } from "@playwright/test";

test("user can sign up and create a post", async ({ page }) => {
  await page.goto("/signup");
  await page.fill("[name=email]", "new@example.com");
  await page.fill("[name=password]", "SecurePass123!");
  await page.click("[type=submit]");
  
  await expect(page).toHaveURL("/dashboard");
  
  await page.click("[data-testid=create-post]");
  await page.fill("[name=title]", "My First Post");
  await page.click("[type=submit]");
  
  await expect(page.getByText("My First Post")).toBeVisible();
});
```

### GitHub Actions CI Pipeline

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "22", cache: "pnpm" }
      
      - run: pnpm install --frozen-lockfile
      - run: pnpm run typecheck
      - run: pnpm run lint
      - run: pnpm run test:unit
      - run: pnpm run db:migrate
        env:
          DATABASE_URL: postgresql://postgres:testpass@localhost/testdb
      - run: pnpm run test:integration
        env:
          DATABASE_URL: postgresql://postgres:testpass@localhost/testdb
      
  e2e:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "22", cache: "pnpm" }
      - run: pnpm install --frozen-lockfile
      - run: npx playwright install --with-deps chromium
      - run: pnpm run build
      - run: pnpm run test:e2e
```

---

## 12. Monorepo Tooling

### Turborepo (Recommended for JS/TS Monorepos)

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"],
      "outputs": [".next/**", "dist/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "inputs": ["src/**", "tests/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  },
  "remoteCache": {
    "enabled": true  // Cache build artifacts across team/CI
  }
}
```

### Monorepo Structure

```
apps/
  web/          # Next.js frontend
  api/          # Hono/Express backend
  docs/         # Docusaurus/Nextra docs site
packages/
  ui/           # Shared component library
  db/           # Prisma/Drizzle schema + client
  types/        # Shared TypeScript types
  utils/        # Shared utilities
  config/       # Shared configs (ESLint, TypeScript, Tailwind)
```

---

## 13. Observability

### OpenTelemetry Stack

```typescript
// instrumentation.ts (Next.js)
import { registerOTel } from "@vercel/otel";

export function register() {
  registerOTel({
    serviceName: "my-app",
    traceExporter: "otlp",
    metricExporter: "otlp",
  });
}
```

### Structured Logging

```typescript
import pino from "pino";

const logger = pino({
  level: process.env.LOG_LEVEL ?? "info",
  transport: process.env.NODE_ENV === "development" 
    ? { target: "pino-pretty" } 
    : undefined,
  base: { service: "my-app", version: process.env.APP_VERSION }
});

// Usage
logger.info({ userId, requestId, duration }, "Request completed");
logger.error({ err, userId, action }, "Database error");
```

**Observability platforms (2025):**
- **Axiom:** Log + traces; Next.js/Vercel native integration; $25/month
- **Datadog:** Full observability; enterprise; expensive but comprehensive
- **Grafana Cloud:** Open-source stack (Prometheus + Loki + Tempo) as managed service; free tier
- **Sentry:** Error tracking + performance; best for exception monitoring

---

## 14. Security Hardening

### OWASP Top 10 Mitigations (2025 Web Edition)

**SQL Injection → Use parameterized queries:**
```typescript
// NEVER string concatenation
const users = await db.execute(sql`SELECT * FROM users WHERE email = ${email}`);
// ORM parameterizes automatically; raw queries require explicit parameterization
```

**XSS → React handles escaping; sanitize rich text:**
```typescript
import DOMPurify from "isomorphic-dompurify";
// For user-generated HTML content
const cleanHtml = DOMPurify.sanitize(userInput, { ALLOWED_TAGS: ["p", "b", "i", "ul", "li"] });
```

**CSRF → SameSite cookies + CSRF tokens:**
```typescript
// Set-Cookie: session=xxx; SameSite=Strict; HttpOnly; Secure; Path=/
// For state-changing non-form requests: verify Origin/Referer header
```

**Security Headers:**
```typescript
// next.config.ts
const securityHeaders = [
  { key: "X-Frame-Options", value: "DENY" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  { key: "Content-Security-Policy", value: "default-src 'self'; script-src 'self' 'unsafe-inline'" },
  { key: "Permissions-Policy", value: "camera=(), microphone=(), geolocation=()" },
  { key: "Strict-Transport-Security", value: "max-age=31536000; includeSubDomains; preload" }
];
```

---

## 15. Performance Optimization

### Core Web Vitals Targets (2025)

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5-4.0s | > 4.0s |
| INP (Interaction to Next Paint) | < 200ms | 200-500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1-0.25 | > 0.25 |

### Performance Patterns

**Image optimization (Next.js):**
```typescript
import Image from "next/image";
// Always use Next.js Image component — automatic:
// WebP/AVIF conversion, lazy loading, responsive sizes, blur placeholder
<Image src="/hero.jpg" width={1200} height={600} priority alt="Hero" />
```

**Bundle optimization:**
```typescript
// Dynamic imports for code splitting
const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <Skeleton />,
  ssr: false  // Only on client if not needed for SSR
});
```

**Database performance:**
- N+1 query prevention: Use `.include()` in Prisma or explicit JOINs in Drizzle
- Connection pooling: Essential for serverless (PgBouncer or Supabase pooler)
- Index all foreign keys and common WHERE/ORDER BY columns
- Use `SELECT` only needed columns — avoid `SELECT *`

---

## 16. AI Integration in Full-Stack Apps

### AI Integration Patterns

**Streaming AI responses to client:**
```typescript
// app/api/chat/route.ts (Next.js Route Handler)
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();

export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const stream = await client.messages.stream({
    model: "claude-haiku-4-5",
    max_tokens: 2048,
    messages
  });
  
  // Return SSE stream
  return new Response(
    new ReadableStream({
      async start(controller) {
        for await (const chunk of stream) {
          if (chunk.type === "content_block_delta" && 
              chunk.delta.type === "text_delta") {
            controller.enqueue(
              new TextEncoder().encode(`data: ${JSON.stringify({ text: chunk.delta.text })}\n\n`)
            );
          }
        }
        controller.enqueue(new TextEncoder().encode("data: [DONE]\n\n"));
        controller.close();
      }
    }),
    { headers: { "Content-Type": "text/event-stream", "Cache-Control": "no-cache" } }
  );
}
```

**AI with tool use in full-stack:**
```typescript
// Define tools that interact with your database
const tools = [{
  name: "get_user_orders",
  description: "Get all orders for a user from the database",
  input_schema: {
    type: "object" as const,
    properties: {
      userId: { type: "string", description: "User ID" },
      status: { type: "string", enum: ["pending", "shipped", "delivered"] }
    },
    required: ["userId"]
  }
}];

// Execute tool calls with real database queries
async function executeTool(name: string, input: Record<string, unknown>) {
  if (name === "get_user_orders") {
    return await db.order.findMany({
      where: { 
        userId: input.userId as string,
        ...(input.status ? { status: input.status as string } : {})
      }
    });
  }
}
```

---

## Quick Reference: Full Stack Decision Matrix

| Decision | Recommendation |
|----------|---------------|
| Frontend framework for SaaS app | Next.js 15 (App Router) |
| Frontend for content site | Astro |
| TypeScript strictness | Enable all strict flags + noUncheckedIndexedAccess |
| Component library | Shadcn/UI (Radix + Tailwind) |
| State management (server) | TanStack Query |
| State management (client) | Zustand |
| API layer (full-stack TypeScript) | tRPC |
| API layer (multi-client) | REST + OpenAPI (Hono/Fastify) |
| ORM (DX-first) | Prisma |
| ORM (performance/edge) | Drizzle |
| Database (primary) | PostgreSQL (Supabase or Neon) |
| Auth (fastest to ship) | Clerk |
| Auth (open-source) | Auth.js v5 |
| Deployment (Next.js) | Vercel |
| Deployment (full-stack/DBs) | Fly.io or Railway |
| Monorepo | Turborepo |
| Unit testing | Vitest |
| E2E testing | Playwright |
| Logging | Pino (structured) |
| Error tracking | Sentry |
| AI streaming | ReadableStream + SSE |
| LLM costs at scale | Prompt caching + model routing |
