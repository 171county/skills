---
name: full-stack-app-expert
description: Expert-level full-stack application development — React 19/Next.js 15-16 (RSC, PPR, Server Actions), TypeScript strict patterns, state management (TanStack Query/Zustand/Jotai), Tailwind v4/shadcn/ui, backend frameworks (Hono/Fastify/FastAPI/Bun), tRPC v11, databases (PostgreSQL/Drizzle ORM/Prisma 7/Redis), auth (Better Auth/Clerk/JWT+refresh), deployment (Vercel/Railway/Fly.io/Cloudflare Workers), testing (Vitest/Playwright/MSW), performance optimization (PPR, bundle optimization, Core Web Vitals), and security (OWASP Top 10 2025). Use when building, architecting, debugging, or optimizing full-stack TypeScript/Python applications.
---

# Full Stack App Expert

## Frontend Frameworks

### React 19 — Current Capabilities

**Actions API**
- `useActionState`: manage async form action state (pending, error, data)
- `useFormStatus`: access parent form submission state without prop drilling
- `useOptimistic`: instant UI updates with automatic rollback on error
- `use()`: consume promises and context anywhere in component tree (no rules of hooks restriction)

```tsx
// Server Action pattern
async function createPost(formData: FormData) {
  'use server'
  const title = formData.get('title') as string
  await db.insert(posts).values({ title })
  revalidatePath('/posts')
}

// Client Component using Action
function PostForm() {
  const [state, formAction, isPending] = useActionState(createPost, null)
  
  return (
    <form action={formAction}>
      <input name="title" disabled={isPending} />
      {state?.error && <p className="text-red-500">{state.error}</p>}
    </form>
  )
}
```

**React Compiler (Stable)**
- Automatic memoization: compiler inserts `useMemo`/`useCallback`/`memo` where needed
- Eliminates most manual memoization
- Opt-out: `"use no memo"` directive for exceptional cases
- Enable via Babel/SWC plugin: `babel-plugin-react-compiler`

### Next.js 15/16

**Caching Changes (Critical Shift)**
- Next.js 15: caching reversed to **no-store by default** (breaking change from v14)
- `fetch()` no longer cached; must opt-in: `fetch(url, { cache: 'force-cache' })`
- Route handlers: no caching by default
- `unstable_cache` for server-side caching with tags

**Partial Pre-Rendering (PPR) — Stable in v16**
- Enable: `cacheComponents: true` in next.config.ts
- Static shell rendered at build time; dynamic regions wrapped in `<Suspense>`
- TTFB: 45ms (PPR) vs 450ms (full dynamic) — 10x improvement
- LCP element MUST be in first static chunk (not inside Suspense)

**PPR Pattern**
```tsx
// page.tsx — static shell with dynamic Suspense boundaries
export default function ProductPage({ params }: { params: { id: string } }) {
  return (
    <div>
      <ProductHeader />  {/* static — rendered at build */}
      <Suspense fallback={<PriceSkeleton />}>
        <DynamicPrice productId={params.id} />  {/* dynamic — streamed */}
      </Suspense>
      <Suspense fallback={<ReviewsSkeleton />}>
        <Reviews productId={params.id} />  {/* dynamic — independent boundary */}
      </Suspense>
    </div>
  )
}

// Each Suspense boundary streams independently
// Don't combine unrelated data fetches in one boundary
```

**Server Components (RSC)**
- Default in Next.js App Router
- No client-side JavaScript; direct DB/filesystem access
- Cannot use: useState, useEffect, browser APIs, event handlers
- Client boundary: `'use client'` at top of file

**Server Actions**
- `'use server'` directive; called like functions from client
- Auto-generate secure RPC endpoints
- Form submissions: `action={myServerAction}`
- Programmatic: `await myServerAction(data)`

### Alternative Frontends

**Remix / React Router v7**
- Unified routing for SPA + SSR; nested routes; parallel data loading
- Best for: data-heavy applications; forms; progressive enhancement

**Astro**
- Zero JavaScript by default; Islands architecture for interactive components
- Best for: content sites, documentation, marketing pages with <5% interactivity

**SvelteKit + Runes**
- Runes ($state, $derived, $effect): explicit reactive primitives
- Smaller bundle, faster hydration than React
- Best for: teams comfortable with Svelte; performance-critical UIs

---

## TypeScript — Expert Patterns

### Strict Config
```json
// tsconfig.json strict additions beyond "strict": true
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,     // arr[0] is T | undefined
    "exactOptionalPropertyTypes": true,    // optional ≠ | undefined unless explicit
    "verbatimModuleSyntax": true,          // import type enforcement
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

### Discriminated Unions (Preferred Over Optional Fields)
```typescript
// WRONG: optional fields create impossible states
type ApiResponse = {
  data?: User
  error?: string
  status: 'success' | 'error'
}

// CORRECT: discriminated union — exhaustive type narrowing
type ApiResponse =
  | { status: 'success'; data: User }
  | { status: 'error'; error: string }
  | { status: 'loading' }

function handle(response: ApiResponse) {
  switch (response.status) {
    case 'success': return response.data.name  // TypeScript knows data exists
    case 'error': return response.error         // TypeScript knows error exists
    case 'loading': return 'Loading...'
  }
  // Exhaustive check: TypeScript errors if case missed
}
```

### The `satisfies` Operator
```typescript
// satisfies: validate against type without widening
const config = {
  db: 'postgresql://localhost/mydb',
  port: 5432,
} satisfies Record<string, string | number>

// config.port is still number (not string | number)
// Without satisfies: would widen to Record<string, string | number>
```

### Type Guards
```typescript
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'email' in value &&
    typeof (value as User).email === 'string'
  )
}
```

---

## State Management

### TanStack Query v5

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

// Query with staleTime
const { data, isLoading, error } = useQuery({
  queryKey: ['users', userId],
  queryFn: () => fetchUser(userId),
  staleTime: 5 * 60 * 1000,  // 5 minutes before refetch
  gcTime: 10 * 60 * 1000,     // 10 minutes before garbage collection
})

// Mutation with cache invalidation
const queryClient = useQueryClient()
const mutation = useMutation({
  mutationFn: updateUser,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['users'] })
  },
  onMutate: async (newUser) => {
    // Optimistic update
    await queryClient.cancelQueries({ queryKey: ['users', newUser.id] })
    const previous = queryClient.getQueryData(['users', newUser.id])
    queryClient.setQueryData(['users', newUser.id], newUser)
    return { previous }
  },
  onError: (err, newUser, context) => {
    queryClient.setQueryData(['users', newUser.id], context?.previous)
  }
})
```

### Zustand
```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'
import { persist } from 'zustand/middleware'

interface CartStore {
  items: CartItem[]
  addItem: (item: CartItem) => void
  removeItem: (id: string) => void
  total: () => number
}

const useCart = create<CartStore>()(
  persist(
    immer((set, get) => ({
      items: [],
      addItem: (item) => set(state => { state.items.push(item) }),
      removeItem: (id) => set(state => {
        state.items = state.items.filter(i => i.id !== id)
      }),
      total: () => get().items.reduce((sum, item) => sum + item.price, 0)
    })),
    { name: 'cart-storage' }
  )
)
```

### Jotai (Atomic State)
```typescript
import { atom, useAtom, useAtomValue } from 'jotai'
import { loadable } from 'jotai/utils'

const userIdAtom = atom<string | null>(null)
const userAtom = atom(async (get) => {
  const id = get(userIdAtom)
  if (!id) return null
  return fetchUser(id)
})
const loadableUser = loadable(userAtom)
```

---

## UI Layer

### Tailwind CSS v4

**Key Changes from v3**
- CSS-first configuration: `@theme` directive in CSS instead of `tailwind.config.js`
- Oxide engine (Rust): 2-5x faster builds
- `bg-linear-to-*` replaces `bg-gradient-to-*`

```css
/* app.css — configure in CSS */
@import "tailwindcss";

@theme {
  --color-brand: oklch(65% 0.25 290);
  --font-sans: "Inter", system-ui, sans-serif;
  --radius-card: 0.75rem;
}
```

**Migration**: `npx @tailwindcss/upgrade` handles most breaking changes automatically

### shadcn/ui
- **Code ownership model**: copy components into your codebase; you own the code
- `npx shadcn@latest add button` → generates `components/ui/button.tsx`
- Based on Radix UI primitives (accessibility) + Tailwind styling
- Components: Button, Dialog, Drawer, DropdownMenu, Form, Input, Select, Table, Toast, etc.
- Customize freely; no version lock-in

### Radix UI (Headless Primitives)
- ARIA-compliant: keyboard navigation, screen reader support built-in
- Use directly when shadcn/ui doesn't have the component you need
- Pattern: Radix for accessibility, Tailwind for styling

---

## Backend Frameworks

### Hono
```typescript
import { Hono } from 'hono'
import { zValidator } from '@hono/zod-validator'
import { z } from 'zod'

const app = new Hono()

// Typed routes with validation
app.post('/users',
  zValidator('json', z.object({ name: z.string(), email: z.string().email() })),
  async (c) => {
    const { name, email } = c.req.valid('json')
    const user = await db.insert(users).values({ name, email }).returning()
    return c.json(user[0], 201)
  }
)

// Works on: Node.js, Bun, Cloudflare Workers, Deno, Fastly
export default app
```

- ~14KB; WinterCG compatible; 4-6K req/sec single instance
- Best for: edge deployment (Cloudflare Workers), serverless, lightweight APIs

### tRPC v11
```typescript
// server/router.ts
import { initTRPC, TRPCError } from '@trpc/server'
import { z } from 'zod'

const t = initTRPC.context<Context>().create()

export const appRouter = t.router({
  users: t.router({
    list: t.procedure
      .input(z.object({ limit: z.number().default(10) }))
      .query(async ({ input, ctx }) => {
        return db.select().from(users).limit(input.limit)
      }),
    create: t.procedure
      .input(z.object({ name: z.string(), email: z.string().email() }))
      .mutation(async ({ input, ctx }) => {
        if (!ctx.session) throw new TRPCError({ code: 'UNAUTHORIZED' })
        return db.insert(users).values(input).returning()
      }),
  })
})

// client/trpc.ts — TypeScript IS the schema (no code generation)
const trpc = createTRPCReact<AppRouter>()
const { data } = trpc.users.list.useQuery({ limit: 20 })
```

- v11: RSC support, Suspense, streaming (observable returns)
- ~10KB; end-to-end type safety with zero duplication
- Best for: TypeScript monorepos, team codebases, rapid prototyping

### FastAPI (Python)
```python
from fastapi import FastAPI, Depends, HTTPException
from pydantic import BaseModel
from typing import Annotated

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: str

@app.post("/users", response_model=User, status_code=201)
async def create_user(user: UserCreate, db: AsyncSession = Depends(get_db)):
    db_user = UserModel(**user.model_dump())
    db.add(db_user)
    await db.commit()
    await db.refresh(db_user)
    return db_user
```

- Auto-generates OpenAPI 3.1 docs
- Pydantic v2: 5-50x faster validation vs v1
- Best for: Python teams, ML model serving, data-heavy backends

### Bun 1.2 / 1.3
- 1.2: `Bun.SQL` (PostgreSQL), `Bun.S3` (S3-compatible)
- 1.3: built-in MySQL client, Redis client
- 3x faster than Node.js for most workloads
- Drop-in Node.js replacement; runs TypeScript natively

---

## Database Layer

### Drizzle ORM
```typescript
import { drizzle } from 'drizzle-orm/postgres-js'
import { pgTable, text, integer, timestamp } from 'drizzle-orm/pg-core'
import { eq, and, gte } from 'drizzle-orm'

const users = pgTable('users', {
  id: text('id').primaryKey().$defaultFn(() => crypto.randomUUID()),
  email: text('email').notNull().unique(),
  createdAt: timestamp('created_at').defaultNow()
})

const db = drizzle(connectionString)

// Type-safe queries — generates single SQL statement
const activeUsers = await db.select()
  .from(users)
  .where(and(eq(users.status, 'active'), gte(users.createdAt, cutoff)))
  .limit(10)
```

- 57KB bundle; cold start 75ms; generates single SQL (no N+1)
- Edge-native: runs in Cloudflare Workers, Vercel Edge
- Migrations: `drizzle-kit generate` + `drizzle-kit migrate`

### Prisma 7
- Rust → TypeScript/WASM rewrite: 14MB → 1.6MB bundle; 1500ms → 115ms cold start
- Now edge-viable (Cloudflare Workers, Vercel Edge)
- Accelerate: connection pooling + global caching (replaces Prisma Data Proxy)
- **When to choose**: teams familiar with Prisma; greenfield projects; complex relations

### PostgreSQL — Production Patterns

**Connection Pooling**
```
Application → PgBouncer (transaction mode) → PostgreSQL

Transaction mode: connection returned to pool after each transaction
Better for serverless/edge where connections are ephemeral
```

**Indexes**
```sql
-- Composite index for common query patterns
CREATE INDEX CONCURRENTLY idx_users_status_created 
  ON users(status, created_at DESC);

-- Partial index for filtered queries  
CREATE INDEX idx_active_users ON users(email) WHERE status = 'active';

-- GIN index for JSONB
CREATE INDEX idx_metadata ON posts USING GIN(metadata);

-- pgvector HNSW for vector search
CREATE INDEX ON embeddings USING hnsw(embedding vector_cosine_ops)
  WITH (m = 16, ef_construction = 64);
```

### Redis Patterns
```typescript
// Cache-aside (most common)
async function getUser(id: string): Promise<User> {
  const cached = await redis.get(`user:${id}`)
  if (cached) return JSON.parse(cached)
  
  const user = await db.select().from(users).where(eq(users.id, id)).first()
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user))  // 1hr TTL
  return user
}

// Job queue with BullMQ
import { Queue, Worker } from 'bullmq'

const emailQueue = new Queue('email', { connection: redisClient })
await emailQueue.add('welcome-email', { userId, email }, { attempts: 3, backoff: { type: 'exponential', delay: 1000 } })

const worker = new Worker('email', async job => {
  await sendEmail(job.data)
}, { connection: redisClient })
```

---

## Authentication

### Better Auth (Current Standard, Sept 2025)
- Replaced Auth.js as the community-preferred self-hosted auth library
- Plugin architecture: 2FA, magic links, passkeys, organizations, SSO
- Built-in adapters: Drizzle, Prisma, MongoDB
```typescript
import { betterAuth } from "better-auth"
import { drizzleAdapter } from "better-auth/adapters/drizzle"
import { twoFactor, organization } from "better-auth/plugins"

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: "pg" }),
  emailAndPassword: { enabled: true },
  socialProviders: {
    github: { clientId: env.GITHUB_CLIENT_ID, clientSecret: env.GITHUB_CLIENT_SECRET },
    google: { clientId: env.GOOGLE_CLIENT_ID, clientSecret: env.GOOGLE_CLIENT_SECRET }
  },
  plugins: [twoFactor(), organization()]
})
```

### JWT + Refresh Token Pattern (API Auth)
```typescript
// Access token: 15min expiry; stateless
const accessToken = jwt.sign({ sub: userId, role }, JWT_SECRET, { expiresIn: '15m' })

// Refresh token: 7-30 days; stored in DB for revocation
const refreshToken = crypto.randomUUID()
await db.insert(refreshTokens).values({ token: hash(refreshToken), userId, expiresAt })

// Set refresh token in httpOnly cookie (XSS-safe)
res.cookie('refresh_token', refreshToken, {
  httpOnly: true,
  secure: true,
  sameSite: 'lax',
  maxAge: 30 * 24 * 60 * 60 * 1000
})
```

---

## Deployment

### Platform Selection Matrix

| Platform | Best For | Notes |
|----------|----------|-------|
| Vercel | Next.js, frontend-heavy | Expensive at scale; unmatched DX |
| Railway | Full-stack, simple ops | 4 regions; PostgreSQL + Redis built-in |
| Fly.io | Stateful apps, global | 35+ locations; persistent volumes; Machines API |
| Cloudflare Workers | Edge compute, serverless | V8 isolates; <1ms cold start; Durable Objects |
| AWS ECS/Fargate | Enterprise, existing AWS | Complex setup; full control |
| AWS Lambda | Serverless, event-driven | Cold start issue; Bun runtime available |

---

## Testing Strategy

### Vitest (Unit/Integration)
```typescript
import { describe, it, expect, vi } from 'vitest'

describe('UserService', () => {
  it('creates user with hashed password', async () => {
    const mockDb = { insert: vi.fn().mockResolvedValue([{ id: '123' }]) }
    const service = new UserService(mockDb as any)
    
    const user = await service.createUser({ email: 'test@example.com', password: 'plaintext' })
    
    expect(mockDb.insert).toHaveBeenCalledOnce()
    expect(user.id).toBe('123')
  })
})
```
- 1.2s boot vs Jest 8s; native TypeScript; compatible with Jest API

### Playwright (E2E)
```typescript
import { test, expect } from '@playwright/test'

test('user can complete checkout', async ({ page }) => {
  await page.goto('/products/123')
  await page.getByRole('button', { name: 'Add to Cart' }).click()
  await page.getByRole('link', { name: 'Checkout' }).click()
  
  await expect(page.getByText('Order Confirmed')).toBeVisible({ timeout: 5000 })
})
```
- Auto-wait: no `waitFor` needed; retries assertions automatically
- Trace viewer: full visual debugging with screenshots/video
- CI: parallel workers; <2min for full E2E suite

### MSW (API Mocking)
```typescript
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  http.get('/api/users/:id', ({ params }) => {
    return HttpResponse.json({ id: params.id, name: 'Test User' })
  })
)

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

---

## Performance Optimization

### Core Web Vitals Targets (Google 2025)
- **LCP** (Largest Contentful Paint): <2.5s (good), <4s (needs improvement)
- **INP** (Interaction to Next Paint, replaced FID March 2024): <200ms (good), <500ms (needs improvement)
- **CLS** (Cumulative Layout Shift): <0.1 (good), <0.25 (needs improvement)

### Bundle Optimization
```typescript
// Dynamic imports — code-split at route level
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false  // skip server render for client-only components
})

// Analyze bundle
import bundleAnalyzer from '@next/bundle-analyzer'
const withBundleAnalyzer = bundleAnalyzer({ enabled: process.env.ANALYZE === 'true' })
```

### Image Optimization
```tsx
import Image from 'next/image'

// next/image: automatic WebP/AVIF, lazy loading, blur placeholder
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={630}
  priority  // LCP image: disable lazy loading
  placeholder="blur"
  blurDataURL={blurUrl}
/>
```

---

## Security (OWASP Top 10 2025)

**A01 — Broken Access Control (#1)**
```typescript
// Middleware: verify resource ownership before access
async function requireOwnership(userId: string, resourceId: string) {
  const resource = await db.select().from(resources).where(eq(resources.id, resourceId)).first()
  if (!resource || resource.userId !== userId) {
    throw new TRPCError({ code: 'FORBIDDEN' })
  }
}
```

**A02 — Security Misconfiguration (jumped to #2)**
- CSP with nonces (not `unsafe-inline`)
- Security headers: HSTS, X-Frame-Options, X-Content-Type-Options
- Remove default error stack traces in production

**Input Validation (Zod)**
```typescript
const userSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100).trim(),
  age: z.number().int().min(0).max(150)
})

// Validate at API boundary — never trust client input
const validated = userSchema.parse(req.body)  // throws ZodError if invalid
```

**Rate Limiting (Redis Sliding Window)**
```typescript
async function rateLimit(key: string, limit: number, windowMs: number): Promise<boolean> {
  const now = Date.now()
  const window = Math.floor(now / windowMs)
  const redisKey = `ratelimit:${key}:${window}`
  
  const count = await redis.incr(redisKey)
  if (count === 1) await redis.expire(redisKey, Math.ceil(windowMs / 1000))
  
  return count <= limit
}
```

---

## Monorepo with Turborepo

```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "dist/**"]
    },
    "test": {
      "dependsOn": ["^build"],
      "cache": false
    }
  }
}
```

- Remote cache: share build artifacts across CI + team
- `--filter='...[origin/main]'`: only run affected packages
- Nx alternative: more precise affected detection; better for large monorepos

---

## AI Feature Integration

### Streaming AI (Vercel AI SDK)
```typescript
import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

// API route
export async function POST(req: Request) {
  const { messages } = await req.json()
  
  const result = await streamText({
    model: anthropic('claude-sonnet-4-6'),
    messages,
    system: 'You are a helpful assistant.',
  })
  
  return result.toDataStreamResponse()  // SSE stream
}

// Client component
const { messages, input, handleSubmit } = useChat({
  api: '/api/chat',
  onError: (error) => toast.error(error.message)
})
```

### Vector Search (pgvector HNSW)
```sql
-- Index sufficient for <1M vectors
CREATE INDEX ON documents USING hnsw(embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64, ef_search = 40);

-- Similarity search
SELECT id, content, 1 - (embedding <=> $1) AS similarity
FROM documents
ORDER BY embedding <=> $1  -- cosine distance
LIMIT 10;
```

---

## Expert Calibration Notes

- **PPR (Partial Pre-Rendering) in Next.js 16 is the single biggest performance unlock for most apps**: every page with any dynamic data benefits; static shell + streaming dynamic Suspense regions
- **Next.js 15 reversed caching to no-store default**: every migration from v14 must audit `fetch()` calls; missing `cache: 'force-cache'` where needed causes unnecessary API calls
- **Drizzle vs Prisma 7**: both are edge-viable now; Drizzle wins on bundle size (57KB vs 1.6MB) and single-SQL query generation; Prisma wins on schema readability and migration tooling maturity
- **Better Auth replaced Auth.js as community standard** in Sept 2025: new projects should default to Better Auth; Auth.js is maintenance mode
- **INP replaced FID in March 2024**: update CWV monitoring and optimization targets; INP measures all interactions (not just first), making it a much more demanding metric
- **tRPC v11 RSC support closes the last gap**: full-stack TypeScript with RSC + tRPC eliminates all API contracts — consider it the default for TypeScript teams building with Next.js App Router
