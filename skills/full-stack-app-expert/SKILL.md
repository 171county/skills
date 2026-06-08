---
name: full-stack-app-expert
description: Expert-level guidance on building production full-stack applications. Use this skill whenever the user asks about React 19 or Next.js 15 (server components, partial prerendering, caching, streaming), TypeScript strict mode and advanced type patterns, state management (TanStack Query v5, Zustand, Jotai), API design with Hono/Fastify/tRPC/FastAPI, database ORM choices (Drizzle vs Prisma 7), authentication (Better Auth, Auth.js), edge deployment (Cloudflare Workers/D1/R2/Pages), testing strategy (Vitest, MSW, Playwright), Vercel AI SDK v5, Turborepo monorepo patterns, observability (Pino, Sentry, PostHog), Docker containerization, CI/CD pipeline design, TypeScript project configuration, or production deployment architecture for modern web applications. This skill is essential when the user is building, architecting, or debugging a full-stack TypeScript/JavaScript or Python web application.
---

# Full Stack App Expert

You are an expert in building production-grade full-stack applications with modern TypeScript/JavaScript and Python stacks. You write real, compilable code and give architecture recommendations based on production experience, not hypothetical preferences.

---

## Part 1: React 19 and Next.js 15 — Current State

### React 19 Key Features

**React Compiler**: Automatic memoization compiler (stable in React 19). Eliminates the need for manual `useMemo`, `useCallback`, `memo` in most cases. The compiler analyzes component code and inserts memoization only where beneficial.

```typescript
// Before React Compiler (had to manually memoize)
const ExpensiveComponent = memo(({ data }: Props) => {
  const processed = useMemo(() => processData(data), [data])
  const handleClick = useCallback(() => doSomething(data), [data])
  return <div onClick={handleClick}>{processed}</div>
})

// With React Compiler — write naturally, compiler handles it
function ExpensiveComponent({ data }: Props) {
  const processed = processData(data)  // compiler memoizes automatically
  return <div onClick={() => doSomething(data)}>{processed}</div>
}
```

**Actions and useTransition**: Async transitions for form submissions and mutations.

```typescript
import { useTransition, useActionState } from 'react'

function ContactForm() {
  const [state, submitAction, isPending] = useActionState(
    async (prevState: FormState, formData: FormData) => {
      const result = await submitContact({
        email: formData.get('email') as string,
        message: formData.get('message') as string
      })
      return result.success ? { success: true } : { error: result.error }
    },
    null
  )
  
  return (
    <form action={submitAction}>
      <input name="email" type="email" required />
      <textarea name="message" required />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Sending...' : 'Send'}
      </button>
      {state?.error && <p className="error">{state.error}</p>}
    </form>
  )
}
```

**use() hook**: Reads resources (promises, context) during render.

```typescript
import { use, Suspense } from 'react'

// Suspense-based data fetching
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise)  // Suspends until promise resolves
  return <div>{user.name}</div>
}

function App() {
  const userPromise = fetchUser(userId)  // Created OUTSIDE the component to avoid recreation
  return (
    <Suspense fallback={<Skeleton />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  )
}
```

### Next.js 15 — Architecture Decisions

**Turbopack**: Now the default for `next dev`. Significantly faster HMR than Webpack. Production builds still use Rollup/webpack as of Next.js 15 stable.

**Partial Prerendering (PPR)**: New rendering mode combining static shell + dynamic holes. Shell is served instantly from CDN; dynamic content streams in. Enabled per-route:
```typescript
// app/dashboard/page.tsx
export const experimental_ppr = true  // Enable PPR for this route

export default function Dashboard() {
  return (
    <div>
      <StaticHeader />  {/* Renders at build time → cached at CDN */}
      <Suspense fallback={<DashboardSkeleton />}>
        <DynamicContent />  {/* Streams in after initial response */}
      </Suspense>
    </div>
  )
}
```

**Caching in Next.js 15** (major changes from v14):

| Cache | Default in Next 15 | Change from v14 |
|---|---|---|
| `fetch()` in RSC | **Not cached** (no-store) | v14 cached by default |
| Route handlers (`GET`) | **Not cached** | v14 cached |
| `generateStaticParams` | **Cached** (same as v14) | No change |
| Client router cache | 30-second expiry for pages | Changed from indefinite |

```typescript
// Explicit opt-in to caching in Next 15
async function fetchProducts() {
  const res = await fetch('/api/products', {
    next: { revalidate: 3600 }  // Cache for 1 hour; opt-in required
  })
  return res.json()
}

// Force static caching
export const dynamic = 'force-static'

// Force dynamic (no caching)
export const dynamic = 'force-dynamic'
```

### Server Components vs Client Components

```typescript
// Server Component (default in Next.js app directory)
// Can: async/await, access DB directly, read server-only env vars
// Cannot: useState, useEffect, event handlers, browser APIs

// app/products/page.tsx (Server Component)
import { db } from '@/lib/db'

export default async function ProductsPage() {
  const products = await db.query.products.findMany()  // Direct DB access
  return <ProductList products={products} />
}

// components/product-list.tsx (Client Component — has interactivity)
'use client'
import { useState } from 'react'

export function ProductList({ products }: { products: Product[] }) {
  const [filter, setFilter] = useState('')
  const filtered = products.filter(p => p.name.includes(filter))
  return (
    <div>
      <input value={filter} onChange={e => setFilter(e.target.value)} />
      {filtered.map(p => <ProductCard key={p.id} product={p} />)}
    </div>
  )
}
```

**Decision rule**: Start with Server Component. Switch to Client Component only when you need: state, effects, event handlers, browser APIs, or third-party libraries that require the client.

**Server Actions** — mutations from the server:
```typescript
// app/actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { db } from '@/lib/db'

export async function createProduct(formData: FormData) {
  const name = formData.get('name') as string
  const price = Number(formData.get('price'))
  
  await db.insert(products).values({ name, price, createdAt: new Date() })
  revalidatePath('/products')
}
```

---

## Part 2: TypeScript Configuration and Patterns

### Strict Mode Configuration

```json
// tsconfig.json — production baseline
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,          // Enables all strict checks below
    "noUncheckedIndexedAccess": true,    // array[i] returns T | undefined
    "noImplicitOverride": true,
    "exactOptionalPropertyTypes": true,
    "noPropertyAccessFromIndexSignature": true,
    "isolatedModules": true,
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Advanced Type Patterns

**Branded types** for type safety on primitive IDs:
```typescript
type Brand<T, B extends string> = T & { readonly __brand: B }
type UserId = Brand<string, 'UserId'>
type OrderId = Brand<string, 'OrderId'>

function getUser(id: UserId): Promise<User> { /* ... */ }

// Type error: cannot pass OrderId where UserId is expected
const orderId = 'ord-123' as OrderId
getUser(orderId)  // Error!
```

**Discriminated unions** for exhaustive type narrowing:
```typescript
type ApiResponse<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: string; code: number }
  | { status: 'loading' }

function render<T>(response: ApiResponse<T>) {
  switch (response.status) {
    case 'success': return <DataView data={response.data} />
    case 'error': return <ErrorView message={response.error} />
    case 'loading': return <Spinner />
    // TypeScript verifies all cases are handled (exhaustive)
  }
}
```

**Satisfies operator** (TS 4.9+):
```typescript
// Validates type without widening to the declared type
const config = {
  port: 3000,
  host: 'localhost',
  db: { url: process.env.DATABASE_URL! }
} satisfies AppConfig  // Validated but keeps literal types
```

**Infer in conditional types**:
```typescript
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T
type UnwrapArray<T> = T extends (infer U)[] ? U : T

// Extracting function return type at any depth
type DeepReturnType<T> = T extends (...args: unknown[]) => Promise<infer R> ? R
  : T extends (...args: unknown[]) => infer R ? R
  : never
```

---

## Part 3: State Management

### TanStack Query v5 (React Query)

The gold standard for server state management. v5 introduced a unified API:

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

// Fetching
function UserProfile({ userId }: { userId: string }) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    staleTime: 5 * 60 * 1000,    // 5 minutes before refetching
    gcTime: 10 * 60 * 1000,      // 10 minutes before garbage collecting
  })
  
  if (isLoading) return <Skeleton />
  if (error) return <ErrorMessage error={error} />
  return <div>{data.name}</div>
}

// Mutations with optimistic updates
function useUpdateUser() {
  const queryClient = useQueryClient()
  
  return useMutation({
    mutationFn: (updates: Partial<User>) => updateUser(updates),
    onMutate: async (updates) => {
      await queryClient.cancelQueries({ queryKey: ['user', updates.id] })
      const previous = queryClient.getQueryData(['user', updates.id])
      queryClient.setQueryData(['user', updates.id], (old: User) => ({ ...old, ...updates }))
      return { previous }  // For rollback
    },
    onError: (err, updates, context) => {
      queryClient.setQueryData(['user', updates.id], context?.previous)
    },
    onSettled: (data, error, updates) => {
      queryClient.invalidateQueries({ queryKey: ['user', updates.id] })
    }
  })
}
```

### Zustand — Client State

```typescript
import { create } from 'zustand'
import { persist, devtools } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

interface CartStore {
  items: CartItem[]
  addItem: (item: CartItem) => void
  removeItem: (id: string) => void
  clearCart: () => void
  total: () => number
}

const useCartStore = create<CartStore>()(
  devtools(
    persist(
      immer((set, get) => ({
        items: [],
        addItem: (item) => set(state => {
          const existing = state.items.find(i => i.id === item.id)
          if (existing) { existing.quantity++ }
          else { state.items.push({ ...item, quantity: 1 }) }
        }),
        removeItem: (id) => set(state => {
          state.items = state.items.filter(i => i.id !== id)
        }),
        clearCart: () => set({ items: [] }),
        total: () => get().items.reduce((sum, i) => sum + i.price * i.quantity, 0)
      })),
      { name: 'cart-storage' }
    )
  )
)
```

**Zustand vs Jotai decision**: Zustand for shared store with multiple related pieces of state (cart, user session, UI state). Jotai for atomic, independent pieces of state where you want React Suspense integration and fine-grained reactivity.

---

## Part 4: API Layer

### Hono — Edge-First API Framework

```typescript
import { Hono } from 'hono'
import { zValidator } from '@hono/zod-validator'
import { z } from 'zod'
import { jwt } from 'hono/jwt'

const app = new Hono()

// JWT middleware
app.use('/api/*', jwt({ secret: process.env.JWT_SECRET! }))

const createProductSchema = z.object({
  name: z.string().min(1).max(100),
  price: z.number().positive(),
  categoryId: z.string().uuid()
})

app.post('/api/products',
  zValidator('json', createProductSchema),
  async (c) => {
    const data = c.req.valid('json')
    const product = await db.insert(products).values(data).returning().get()
    return c.json({ success: true, data: product }, 201)
  }
)

// Error handling
app.onError((err, c) => {
  console.error(err)
  return c.json({ success: false, error: err.message }, 500)
})

// Deploy to Cloudflare Workers or any edge runtime
export default app
```

### tRPC — End-to-End Type Safety

```typescript
// server/trpc/router.ts
import { initTRPC, TRPCError } from '@trpc/server'
import { z } from 'zod'
import superjson from 'superjson'

const t = initTRPC.context<Context>().create({ transformer: superjson })

export const createTRPCRouter = t.router
export const publicProcedure = t.procedure
export const protectedProcedure = t.procedure.use(({ ctx, next }) => {
  if (!ctx.session?.user) throw new TRPCError({ code: 'UNAUTHORIZED' })
  return next({ ctx: { ...ctx, session: ctx.session } })
})

export const appRouter = createTRPCRouter({
  products: createTRPCRouter({
    list: publicProcedure
      .input(z.object({ categoryId: z.string().optional(), page: z.number().default(1) }))
      .query(async ({ ctx, input }) => {
        const { categoryId, page } = input
        return ctx.db.query.products.findMany({
          where: categoryId ? eq(products.categoryId, categoryId) : undefined,
          limit: 20,
          offset: (page - 1) * 20,
          with: { category: true }
        })
      }),
    
    create: protectedProcedure
      .input(createProductSchema)
      .mutation(async ({ ctx, input }) => {
        return ctx.db.insert(products).values(input).returning().get()
      })
  })
})

export type AppRouter = typeof appRouter

// client/api.ts — fully typed client
import { createTRPCReact } from '@trpc/react-query'
import type { AppRouter } from '@/server/trpc/router'

export const api = createTRPCReact<AppRouter>()

// Usage in component — full autocomplete + type inference
function ProductsPage() {
  const { data } = api.products.list.useQuery({ page: 1 })
  const createProduct = api.products.create.useMutation()
  // data is typed as Product[] automatically
}
```

---

## Part 5: Database Layer

### Drizzle ORM vs Prisma 7

| Criterion | Drizzle | Prisma 7 |
|---|---|---|
| **TypeScript** | Schema in TypeScript (no migration from schema) | Schema in .prisma DSL, generates TS types |
| **Edge compatibility** | Full edge runtime support | Prisma 7 added edge support (Accelerate required for some) |
| **Performance** | Thin SQL wrapper; near-raw SQL speed | Generated client; slightly more overhead |
| **Migrations** | Drizzle Kit generates SQL migrations | Prisma Migrate |
| **Type safety** | Schema is the types | Schema → generated types |
| **Learning curve** | Lower if you know SQL | Higher; own DSL |
| **Ecosystem** | Growing fast | Mature, large ecosystem |

**Recommendation**: Drizzle for new greenfield projects (especially edge deployments); Prisma 7 if team already knows it or you need its advanced features (nested transactions, rich computed fields).

### Drizzle with Postgres

```typescript
// db/schema.ts
import { pgTable, text, timestamp, uuid, numeric, index } from 'drizzle-orm/pg-core'
import { relations } from 'drizzle-orm'

export const products = pgTable('products', {
  id: uuid('id').defaultRandom().primaryKey(),
  name: text('name').notNull(),
  price: numeric('price', { precision: 10, scale: 2 }).notNull(),
  categoryId: uuid('category_id').notNull().references(() => categories.id),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull()
}, (t) => ({
  nameIdx: index('products_name_idx').on(t.name),
  categoryIdx: index('products_category_idx').on(t.categoryId)
}))

export const productsRelations = relations(products, ({ one }) => ({
  category: one(categories, {
    fields: [products.categoryId],
    references: [categories.id]
  })
}))

// db/index.ts
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'
import * as schema from './schema'

const client = postgres(process.env.DATABASE_URL!)
export const db = drizzle(client, { schema })
```

---

## Part 6: Authentication — Better Auth

```typescript
// lib/auth.ts
import { betterAuth } from 'better-auth'
import { drizzleAdapter } from 'better-auth/adapters/drizzle'
import { db } from '@/db'

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: 'pg' }),
  
  emailAndPassword: { enabled: true },
  
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!
    },
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!
    }
  },
  
  plugins: [
    // Organization/multi-tenant support
    organization(),
    // Two-factor auth
    twoFactor(),
    // Admin panel
    admin()
  ]
})

// Types
export type Session = typeof auth.$Infer.Session
export type User = typeof auth.$Infer.Session.user

// app/api/auth/[...all]/route.ts
import { auth } from '@/lib/auth'
export const { GET, POST } = auth.handler
```

---

## Part 7: Edge Deployment — Cloudflare Workers

### Worker + D1 (SQLite at Edge) + R2 (Object Storage)

```typescript
// worker.ts
import { Hono } from 'hono'
import { drizzle } from 'drizzle-orm/d1'
import * as schema from './schema'

interface Bindings {
  DB: D1Database
  BUCKET: R2Bucket
  KV: KVNamespace
  QUEUE: Queue
}

const app = new Hono<{ Bindings: Bindings }>()

app.get('/api/products', async (c) => {
  const db = drizzle(c.env.DB, { schema })
  const products = await db.select().from(schema.products).limit(20)
  return c.json(products)
})

app.post('/api/upload', async (c) => {
  const formData = await c.req.formData()
  const file = formData.get('file') as File
  
  const key = `uploads/${crypto.randomUUID()}-${file.name}`
  await c.env.BUCKET.put(key, file.stream(), {
    httpMetadata: { contentType: file.type }
  })
  
  return c.json({ url: `https://assets.example.com/${key}` })
})

export default app
```

```toml
# wrangler.toml
name = "my-api"
main = "src/worker.ts"
compatibility_date = "2025-01-01"

[[d1_databases]]
binding = "DB"
database_name = "my-db"
database_id = "..."

[[r2_buckets]]
binding = "BUCKET"
bucket_name = "my-assets"

[[kv_namespaces]]
binding = "KV"
id = "..."
```

---

## Part 8: Testing Strategy

### Vitest — Unit and Integration Tests

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'happy-dom',
    globals: true,
    coverage: {
      provider: 'v8',
      thresholds: { branches: 80, functions: 80, lines: 80 }
    }
  }
})
```

### MSW (Mock Service Worker) for API Mocking

```typescript
// src/mocks/handlers.ts
import { http, HttpResponse } from 'msw'

export const handlers = [
  http.get('/api/products', () => {
    return HttpResponse.json([
      { id: '1', name: 'Product A', price: 29.99 }
    ])
  }),
  
  http.post('/api/products', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json({ id: '2', ...body }, { status: 201 })
  })
]

// In tests
import { setupServer } from 'msw/node'
const server = setupServer(...handlers)
beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

### Playwright — E2E Tests

```typescript
// e2e/products.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Products', () => {
  test('user can browse and filter products', async ({ page }) => {
    await page.goto('/products')
    
    // Check products load
    await expect(page.getByRole('list', { name: 'Products' })).toBeVisible()
    const items = page.getByRole('listitem')
    await expect(items).toHaveCount(20)
    
    // Filter by category
    await page.getByLabel('Category').selectOption('Electronics')
    await expect(items).not.toHaveCount(20)  // Different count after filter
    
    // Take screenshot for visual regression
    await expect(page).toHaveScreenshot('products-filtered.png')
  })
})
```

---

## Part 9: Vercel AI SDK v5

```typescript
import { streamText, generateText, generateObject } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
import { z } from 'zod'

// Streaming chat
export async function POST(req: Request) {
  const { messages } = await req.json()
  
  const result = streamText({
    model: anthropic('claude-sonnet-4-6'),
    system: 'You are a helpful assistant.',
    messages,
    tools: {
      weather: {
        description: 'Get current weather for a city',
        parameters: z.object({ city: z.string() }),
        execute: async ({ city }) => fetchWeather(city)
      }
    }
  })
  
  return result.toDataStreamResponse()
}

// Structured generation
const extractedData = await generateObject({
  model: anthropic('claude-sonnet-4-6'),
  schema: z.object({
    title: z.string(),
    summary: z.string(),
    tags: z.array(z.string()),
    sentiment: z.enum(['positive', 'neutral', 'negative'])
  }),
  prompt: `Extract metadata from this article: ${articleText}`
})

// Client-side React hook
import { useChat } from '@ai-sdk/react'

function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
    onError: (error) => console.error('Chat error:', error)
  })
  
  return (
    <div>
      {messages.map(m => <Message key={m.id} message={m} />)}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} placeholder="Ask anything..." />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  )
}
```

---

## Part 10: Observability

### Pino — Structured Logging

```typescript
import pino from 'pino'

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: process.env.NODE_ENV === 'development' ? {
    target: 'pino-pretty',
    options: { colorize: true }
  } : undefined,
  serializers: {
    req: (req) => ({ method: req.method, url: req.url, id: req.id }),
    err: pino.stdSerializers.err
  }
})

// Request logging middleware (Hono)
app.use('*', async (c, next) => {
  const start = Date.now()
  await next()
  logger.info({
    method: c.req.method,
    path: c.req.path,
    status: c.res.status,
    duration: Date.now() - start
  }, 'request')
})
```

### Error Monitoring — Sentry

```typescript
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: process.env.NODE_ENV === 'production' ? 0.1 : 1.0,
  integrations: [
    Sentry.replayIntegration({
      maskAllText: true,  // PII protection
      blockAllMedia: true
    })
  ]
})

// Capture errors with context
try {
  await riskyOperation()
} catch (error) {
  Sentry.withScope(scope => {
    scope.setUser({ id: userId })
    scope.setContext('operation', { type: 'payment', orderId })
    Sentry.captureException(error)
  })
  throw error
}
```

---

## Part 11: Monorepo with Turborepo

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "dist/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "test": {
      "dependsOn": ["^build"],
      "outputs": ["coverage/**"]
    },
    "lint": {
      "outputs": []
    },
    "type-check": {
      "dependsOn": ["^build"],
      "outputs": []
    }
  }
}
```

```
apps/
  web/          # Next.js frontend
  api/          # Hono/Fastify API
  docs/         # Documentation site
packages/
  ui/           # Shared React components
  db/           # Database schema and client
  config/       # Shared ESLint, TypeScript configs
  utils/        # Shared utilities
  types/        # Shared TypeScript types
```

---

## Part 12: Production Deployment Checklist

### Before Going Live
- [ ] TypeScript strict mode: no `any`, no ts-ignore without comment
- [ ] Environment variable validation (Zod schema at startup)
- [ ] Error boundaries in all major React trees
- [ ] Structured logging to centralized system (Pino → Axiom/Datadog)
- [ ] Sentry error tracking configured with source maps
- [ ] Content Security Policy headers
- [ ] CSRF protection on all state-mutating endpoints
- [ ] Rate limiting on auth and API endpoints
- [ ] Database connection pooling (PgBouncer or built-in pool)
- [ ] Database query timeouts configured
- [ ] Dependency vulnerability scanning in CI (`npm audit`, Snyk)
- [ ] E2E tests covering critical user paths
- [ ] Performance budget (Lighthouse CI in GitHub Actions)
- [ ] Rollback procedure documented and tested

---

## Engagement Protocol

When advising on full-stack development:
1. **Write real code** — no pseudocode; production-ready patterns with correct imports
2. **Cite current versions** — specify exact library versions; the ecosystem changes fast
3. **Explain the tradeoff** — when there are two good options (Drizzle vs Prisma), explain the decision criteria
4. **Security by default** — every API endpoint should include auth, input validation, rate limiting
5. **Start with the simplest thing** — don't over-engineer; add complexity only when the simple version proves insufficient

For any architecture question, provide: component diagram, key technology choices with rationale, and the specific code patterns for the core implementation.
