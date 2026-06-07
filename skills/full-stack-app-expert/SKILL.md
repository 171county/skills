---
name: full-stack-app-expert
description: Activate this skill when the user asks about full-stack web application development, modern frontend frameworks, backend API design, database architecture, authentication systems, deployment infrastructure, testing strategies, or production-ready application architecture. Covers Next.js 15 (App Router, React Server Components, Turbopack, PPR), Vue 3/Nuxt 3, Svelte 5 Runes, Astro island architecture, TypeScript 5.x mastery (generics, utility types, satisfies operator, template literal types), state management (TanStack Query, Zustand, Jotai), backend frameworks (Hono, Fastify, NestJS, FastAPI, Go Fiber/Chi, Rust Axum), API design (tRPC, GraphQL, REST, gRPC), database layer (PostgreSQL with indexing, Drizzle ORM, Prisma v7, pgvector), authentication (Better Auth, Clerk, JWT vs sessions, RBAC, OAuth 2.0/OIDC with PKCE), infrastructure (Vercel, Fly.io, Railway, Docker multi-stage builds, Terraform), tooling (Vite, Turborepo, Biome), testing (Vitest, React Testing Library, MSW, Playwright E2E), mobile (React Native New Architecture, Expo SDK 52+, Flutter Impeller), real-time (SSE with Next.js, WebSockets with Redis Pub/Sub), Core Web Vitals (LCP/INP/CLS thresholds), Redis caching patterns, OWASP Top 10 2025 security mitigations, Vercel AI SDK v5 (streamText, useChat, generateObject), OpenTelemetry instrumentation, and production architecture patterns. Use for architectural guidance, code review, technology selection, performance optimization, and building full-stack applications from scratch.
---

# Full Stack App Expert

You are a world-class full-stack engineer with mastery across the entire modern web application stack. You design and build production-grade applications that are fast, maintainable, secure, and scalable. You make opinionated technology choices backed by reasoning, write clean TypeScript code, and understand the tradeoffs between every layer of the stack.

---

## 1. Frontend Framework Selection

### Framework Decision Matrix (2025)

| Framework | Best For | Key Strength | When to Avoid |
|---|---|---|---|
| **Next.js 15** | Full-stack web apps, SEO-required | RSC, hybrid rendering, Vercel ecosystem | Pure SPA; no SSR needed |
| **Vue 3 / Nuxt 3** | Teams from non-React background | Composition API, gentler learning curve | Large React ecosystem needed |
| **Svelte 5 / SvelteKit** | Performance-critical apps | Compiler output; no virtual DOM | Large team (smaller ecosystem) |
| **Solid.js** | Maximum client-side performance | Fine-grained reactivity; no re-renders | Ecosystem maturity |
| **Astro** | Content-heavy sites, MPA | Island architecture; zero JS default | Complex app state |
| **Remix** | Form-heavy, progressive enhancement | Web standards; nested routing | Vercel-centric deployments |

### Next.js 15 Deep Dive

**Key features**:
- Turbopack is now the default dev bundler (97% of tests passing; ~10× faster HMR than webpack)
- React 19 + React Compiler (automatic memoization — no more `useMemo`/`useCallback` everywhere)
- Partial Prerendering (PPR) — stable in 15.1+
- `after()` API for post-response work (logging, analytics)
- `forbidden()` and `unauthorized()` functions in Server Actions

**Rendering Strategies**:
```typescript
// Static generation (default for pages without dynamic data)
// app/products/page.tsx
export default async function ProductsPage() {
  const products = await db.products.findMany();  // Runs at build time
  return <ProductGrid products={products} />;
}

// Dynamic rendering (opts into per-request rendering)
import { unstable_noStore as noStore } from 'next/cache';

export default async function Dashboard() {
  noStore();  // Forces dynamic rendering
  const data = await fetchRealtimeData();
  return <DashboardView data={data} />;
}

// Incremental Static Regeneration (ISR)
export const revalidate = 3600;  // Regenerate every hour

// On-demand revalidation
import { revalidatePath, revalidateTag } from 'next/cache';

export async function updateProduct(id: string, data: ProductUpdate) {
  await db.products.update({ where: { id }, data });
  revalidatePath('/products');
  revalidateTag('products');
}

// Partial Prerendering (PPR) — static shell + dynamic holes
import { Suspense } from 'react';

export const experimental_ppr = true;

export default function ProductPage() {
  return (
    <div>
      <StaticHeader />  {/* Pre-rendered */}
      <Suspense fallback={<Skeleton />}>
        <DynamicRecommendations />  {/* Streamed from server */}
      </Suspense>
    </div>
  );
}
```

**React Server Components (RSC) Pattern**:
```typescript
// Server Component (default in App Router)
// app/users/[id]/page.tsx
async function UserProfile({ params }: { params: { id: string } }) {
  // Direct DB access; never sent to client
  const user = await prisma.user.findUnique({
    where: { id: params.id },
    include: { posts: { take: 5 } }
  });
  
  if (!user) notFound();
  
  return (
    <div>
      <UserCard user={user} />
      {/* Client component for interactivity */}
      <FollowButton userId={user.id} initialFollowing={user.isFollowed} />
    </div>
  );
}

// Client Component (explicit opt-in)
'use client';

import { useState, useTransition } from 'react';

function FollowButton({ userId, initialFollowing }: FollowButtonProps) {
  const [following, setFollowing] = useState(initialFollowing);
  const [isPending, startTransition] = useTransition();
  
  const handleFollow = () => {
    startTransition(async () => {
      await toggleFollow(userId);  // Server action
      setFollowing(prev => !prev);
    });
  };
  
  return (
    <button onClick={handleFollow} disabled={isPending}>
      {following ? 'Unfollow' : 'Follow'}
    </button>
  );
}
```

---

## 2. TypeScript Mastery

### Advanced TypeScript Patterns

**Generic Constraints**:
```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Conditional types
type IsArray<T> = T extends any[] ? true : false;
type Flatten<T> = T extends Array<infer U> ? U : T;

// Template literal types
type EventName = 'click' | 'focus' | 'blur';
type HandlerName = `on${Capitalize<EventName>}`;  // 'onClick' | 'onFocus' | 'onBlur'

// Discriminated union
type Result<T> = 
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error }
  | { status: 'loading' };

function handleResult<T>(result: Result<T>) {
  switch (result.status) {
    case 'success': return result.data;  // TypeScript knows T
    case 'error': throw result.error;    // TypeScript knows Error
    case 'loading': return null;
  }
}
```

**`satisfies` operator (TypeScript 4.9+)**:
```typescript
// satisfies: validate type without widening
const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
  blue: [0, 0, 255],
} satisfies Record<string, string | number[]>;

// palette.red is number[] (not string | number[])
// palette.green is string (not string | number[])
```

**Utility Types**:
```typescript
type User = { id: string; name: string; email: string; age: number };

type PartialUser = Partial<User>;         // All optional
type RequiredUser = Required<User>;       // All required
type ReadonlyUser = Readonly<User>;       // All readonly
type UserPreview = Pick<User, 'id' | 'name'>;      // Subset
type UserWithoutEmail = Omit<User, 'email'>;       // Exclusion
type StringRecord = Record<string, string>;         // Key-value map
type UserOrNull = User | null;
type NonNullUser = NonNullable<UserOrNull>;         // Remove null/undefined
```

**`tsconfig.json` Production Settings**:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "allowImportingTsExtensions": true,
    "verbatimModuleSyntax": true,
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

## 3. State Management

### TanStack Query (Server State)

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Query with optimistic updates
function ProductList() {
  const queryClient = useQueryClient();
  
  const { data: products, isLoading } = useQuery({
    queryKey: ['products', { category: 'electronics' }],
    queryFn: () => fetchProducts({ category: 'electronics' }),
    staleTime: 5 * 60 * 1000,  // 5 minutes
    gcTime: 10 * 60 * 1000,    // 10 minutes (cache eviction)
  });
  
  const deleteMutation = useMutation({
    mutationFn: (id: string) => deleteProduct(id),
    
    // Optimistic update
    onMutate: async (deletedId) => {
      await queryClient.cancelQueries({ queryKey: ['products'] });
      const previous = queryClient.getQueryData(['products']);
      
      queryClient.setQueryData(['products'], (old: Product[]) =>
        old.filter(p => p.id !== deletedId)
      );
      
      return { previous };  // Context for rollback
    },
    
    onError: (err, id, context) => {
      queryClient.setQueryData(['products'], context?.previous);
    },
    
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['products'] });
    },
  });
  
  if (isLoading) return <Skeleton />;
  
  return (
    <ul>
      {products?.map(p => (
        <li key={p.id}>
          {p.name}
          <button onClick={() => deleteMutation.mutate(p.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
```

### Zustand (Client State)

```typescript
import { create } from 'zustand';
import { persist, devtools, immer } from 'zustand/middleware';

interface CartState {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (id: string) => void;
  clearCart: () => void;
  total: () => number;
}

const useCartStore = create<CartState>()(
  devtools(
    persist(
      immer((set, get) => ({
        items: [],
        
        addItem: (item) => set(state => {
          const existing = state.items.find(i => i.id === item.id);
          if (existing) {
            existing.quantity += 1;
          } else {
            state.items.push({ ...item, quantity: 1 });
          }
        }),
        
        removeItem: (id) => set(state => {
          state.items = state.items.filter(i => i.id !== id);
        }),
        
        clearCart: () => set({ items: [] }),
        
        total: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),
      })),
      { name: 'cart-storage' }
    )
  )
);
```

---

## 4. Backend Frameworks

### Hono (High-Performance, Edge-Ready)

```typescript
import { Hono } from 'hono';
import { zValidator } from '@hono/zod-validator';
import { jwt } from 'hono/jwt';
import { z } from 'zod';

const app = new Hono();

// JWT middleware
app.use('/api/*', jwt({ secret: process.env.JWT_SECRET! }));

// Route with validation
const createUserSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
});

app.post('/api/users', 
  zValidator('json', createUserSchema),
  async (c) => {
    const { name, email } = c.req.valid('json');
    const payload = c.get('jwtPayload');  // Typed JWT payload
    
    const user = await db.users.create({ data: { name, email } });
    
    return c.json({ success: true, user }, 201);
  }
);

// Error handling
app.onError((err, c) => {
  console.error(err);
  return c.json({ error: 'Internal Server Error' }, 500);
});

export default app;  // Works on Cloudflare Workers, Vercel Edge, Bun, Node.js
```

### tRPC (Type-Safe API)

```typescript
// server/router.ts
import { router, publicProcedure, protectedProcedure } from './trpc';
import { z } from 'zod';

export const appRouter = router({
  posts: router({
    list: publicProcedure
      .input(z.object({
        cursor: z.string().optional(),
        limit: z.number().min(1).max(50).default(20),
      }))
      .query(async ({ input, ctx }) => {
        const posts = await ctx.db.post.findMany({
          take: input.limit + 1,
          cursor: input.cursor ? { id: input.cursor } : undefined,
          orderBy: { createdAt: 'desc' },
        });
        
        const nextCursor = posts.length > input.limit ? posts.pop()!.id : undefined;
        return { posts, nextCursor };
      }),
    
    create: protectedProcedure
      .input(z.object({
        title: z.string().min(1).max(200),
        content: z.string().min(1),
      }))
      .mutation(async ({ input, ctx }) => {
        return ctx.db.post.create({
          data: { ...input, authorId: ctx.session.userId },
        });
      }),
  }),
});

export type AppRouter = typeof appRouter;

// client/api.ts — Full end-to-end type safety
import { createTRPCReact } from '@trpc/react-query';
const trpc = createTRPCReact<AppRouter>();

// Usage in component
const { data } = trpc.posts.list.useQuery({ limit: 10 });
// data is fully typed; IntelliSense shows exactly what's in each post
```

---

## 5. Database Layer

### PostgreSQL Indexing

```sql
-- B-tree index (default) — equality and range queries
CREATE INDEX idx_users_email ON users(email);

-- Partial index — index only relevant subset
CREATE INDEX idx_active_users ON users(email) WHERE deleted_at IS NULL;

-- Composite index — column order matters
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);

-- GIN index — for array/jsonb/full-text search
CREATE INDEX idx_posts_tags ON posts USING GIN(tags);
CREATE INDEX idx_posts_fts ON posts USING GIN(to_tsvector('english', title || ' ' || content));

-- BRIN index — for naturally ordered data (timestamps, sequential IDs)
CREATE INDEX idx_events_time ON events USING BRIN(created_at);

-- Expression index
CREATE INDEX idx_users_email_lower ON users(lower(email));

-- pgvector for AI embeddings
CREATE EXTENSION vector;
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  content TEXT,
  embedding vector(1536)
);
CREATE INDEX idx_documents_embedding ON documents 
  USING hnsw (embedding vector_cosine_ops)
  WITH (m = 16, ef_construction = 64);

-- Approximate nearest neighbor search
SELECT id, content, embedding <=> '[0.1, 0.2, ...]'::vector AS distance
FROM documents
ORDER BY distance
LIMIT 5;
```

### Drizzle ORM

```typescript
import { pgTable, text, integer, timestamp, boolean } from 'drizzle-orm/pg-core';
import { relations } from 'drizzle-orm';
import { drizzle } from 'drizzle-orm/node-postgres';

// Schema definition
export const users = pgTable('users', {
  id: text('id').primaryKey().$defaultFn(() => crypto.randomUUID()),
  name: text('name').notNull(),
  email: text('email').notNull().unique(),
  emailVerified: boolean('email_verified').notNull().default(false),
  createdAt: timestamp('created_at').notNull().defaultNow(),
  updatedAt: timestamp('updated_at').notNull().$onUpdate(() => new Date()),
});

export const posts = pgTable('posts', {
  id: text('id').primaryKey().$defaultFn(() => crypto.randomUUID()),
  title: text('title').notNull(),
  content: text('content').notNull(),
  authorId: text('author_id').notNull().references(() => users.id, { onDelete: 'cascade' }),
  publishedAt: timestamp('published_at'),
  createdAt: timestamp('created_at').notNull().defaultNow(),
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] }),
}));

// Usage
const db = drizzle(pool, { schema: { users, posts } });

// Type-safe queries
const userWithPosts = await db.query.users.findFirst({
  where: eq(users.email, 'user@example.com'),
  with: {
    posts: {
      where: isNotNull(posts.publishedAt),
      orderBy: desc(posts.createdAt),
      limit: 5,
    },
  },
});
// userWithPosts is fully typed: User & { posts: Post[] }
```

---

## 6. Authentication

### Better Auth Implementation

```typescript
// lib/auth.ts
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { twoFactor, passkey, organization } from "better-auth/plugins";

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: "pg",
    schema: { users, sessions, accounts, verifications },
  }),
  
  emailAndPassword: { enabled: true },
  
  socialProviders: {
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
  },
  
  plugins: [twoFactor(), passkey(), organization()],
  
  session: {
    expiresIn: 60 * 60 * 24 * 7,  // 7 days
    updateAge: 60 * 60 * 24,       // Refresh if older than 1 day
    cookieCache: { enabled: true, maxAge: 5 * 60 },  // 5-min client cache
  },
});

// RBAC Implementation
export async function checkPermission(
  userId: string,
  resource: string,
  action: string
): Promise<boolean> {
  const userRole = await db.userRoles.findFirst({
    where: eq(userRoles.userId, userId),
    with: { role: { with: { permissions: true } } },
  });
  
  return userRole?.role.permissions.some(
    p => p.resource === resource && p.action === action
  ) ?? false;
}
```

---

## 7. Testing Stack

### Vitest Configuration

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import { resolve } from 'path';

export default defineConfig({
  test: {
    environment: 'node',
    globals: true,
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'lcov'],
      thresholds: { lines: 80, functions: 80, branches: 70, statements: 80 },
      exclude: ['**/*.test.ts', '**/__mocks__/**'],
    },
  },
  resolve: {
    alias: { '@': resolve(__dirname, './src') },
  },
});
```

### React Testing Library

```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { expect, test, vi } from 'vitest';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

function renderWithProviders(ui: React.ReactElement) {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  });
  
  return render(
    <QueryClientProvider client={queryClient}>
      {ui}
    </QueryClientProvider>
  );
}

test('user can add item to cart', async () => {
  const user = userEvent.setup();
  renderWithProviders(<ProductCard product={mockProduct} />);
  
  const addButton = screen.getByRole('button', { name: /add to cart/i });
  await user.click(addButton);
  
  expect(screen.getByText(/1 item in cart/i)).toBeInTheDocument();
});
```

### MSW (Mock Service Worker) for API Mocking

```typescript
// src/test/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/products', ({ request }) => {
    const url = new URL(request.url);
    const category = url.searchParams.get('category');
    
    return HttpResponse.json({
      products: mockProducts.filter(p => !category || p.category === category),
    });
  }),
  
  http.post('/api/orders', async ({ request }) => {
    const body = await request.json() as CreateOrderRequest;
    
    if (!body.items?.length) {
      return HttpResponse.json({ error: 'No items' }, { status: 400 });
    }
    
    return HttpResponse.json({ orderId: 'test-123' }, { status: 201 });
  }),
];

// src/test/setup.ts
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

### Playwright E2E Tests

```typescript
// e2e/checkout.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Checkout flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/');
    // Seed test data via API
    await page.request.post('/api/test/seed', { data: { scenario: 'checkout' } });
  });

  test('user can complete purchase', async ({ page }) => {
    await page.goto('/products');
    await page.getByTestId('product-card').first().click();
    await page.getByRole('button', { name: 'Add to Cart' }).click();
    
    await expect(page.getByTestId('cart-count')).toHaveText('1');
    
    await page.goto('/checkout');
    await page.getByLabel('Card number').fill('4242 4242 4242 4242');
    await page.getByLabel('Expiry').fill('12/27');
    await page.getByLabel('CVC').fill('123');
    
    await page.getByRole('button', { name: 'Complete Purchase' }).click();
    
    await expect(page).toHaveURL(/\/orders\/[a-z0-9-]+\/confirmation/);
    await expect(page.getByText('Order confirmed!')).toBeVisible();
  });
});
```

**Testing Pyramid (70/20/10 rule)**:
- 70% unit tests (fast, isolated, pure logic)
- 20% integration tests (components + API mocks)
- 10% E2E tests (critical user journeys only)

---

## 8. Performance & Core Web Vitals

### Thresholds (Google 2025)

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | ≤2.5s | 2.5-4.0s | >4.0s |
| **INP** (Interaction to Next Paint) | ≤200ms | 200-500ms | >500ms |
| **CLS** (Cumulative Layout Shift) | ≤0.1 | 0.1-0.25 | >0.25 |
| **FCP** (First Contentful Paint) | ≤1.8s | 1.8-3.0s | >3.0s |
| **TTFB** (Time to First Byte) | ≤800ms | 800ms-1.8s | >1.8s |

### Redis Caching Patterns

```typescript
import { Redis } from '@upstash/redis';

const redis = new Redis({ url: process.env.UPSTASH_URL!, token: process.env.UPSTASH_TOKEN! });

// Cache-Aside Pattern with stampede prevention
async function getProductWithCache(productId: string): Promise<Product> {
  const key = `product:${productId}`;
  const lockKey = `lock:${key}`;
  
  // Check cache
  const cached = await redis.get<Product>(key);
  if (cached) return cached;
  
  // Acquire lock (prevent cache stampede)
  const lock = await redis.set(lockKey, '1', { nx: true, ex: 30 });
  if (!lock) {
    // Another worker is fetching; wait and retry from cache
    await new Promise(r => setTimeout(r, 100));
    return getProductWithCache(productId);
  }
  
  try {
    const product = await db.products.findUniqueOrThrow({ where: { id: productId } });
    await redis.setex(key, 3600, product);  // Cache 1 hour
    return product;
  } finally {
    await redis.del(lockKey);  // Release lock
  }
}

// Write-Through Pattern
async function updateProduct(id: string, data: ProductUpdate): Promise<Product> {
  const product = await db.products.update({ where: { id }, data });
  await redis.setex(`product:${id}`, 3600, product);  // Update cache immediately
  return product;
}
```

---

## 9. Vercel AI SDK v5 (AI Integration)

### Core Patterns

```typescript
import { streamText, generateObject, tool } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';
import { z } from 'zod';

// Streaming text generation
export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic('claude-sonnet-4-6'),
    system: 'You are a helpful customer support agent for TechCorp.',
    messages,
    tools: {
      searchKnowledgeBase: tool({
        description: 'Search the support knowledge base for relevant articles',
        parameters: z.object({
          query: z.string().describe('Search query'),
          category: z.enum(['billing', 'technical', 'account']).optional(),
        }),
        execute: async ({ query, category }) => {
          return searchDocs(query, category);
        },
      }),
      createSupportTicket: tool({
        description: 'Create a support ticket for issues that cannot be resolved immediately',
        parameters: z.object({
          subject: z.string(),
          description: z.string(),
          priority: z.enum(['low', 'medium', 'high']),
        }),
        execute: async (params) => {
          const ticket = await db.tickets.create({ data: params });
          return { ticketId: ticket.id, message: 'Ticket created successfully' };
        },
      }),
    },
    maxSteps: 5,  // Allow up to 5 agentic steps
  });
  
  return result.toDataStreamResponse();
}

// Structured data extraction
const extractedData = await generateObject({
  model: anthropic('claude-haiku-4-5'),
  schema: z.object({
    sentiment: z.enum(['positive', 'negative', 'neutral']),
    topics: z.array(z.string()),
    urgency: z.number().min(1).max(10),
    suggestedResponse: z.string(),
  }),
  prompt: `Analyze this customer email: ${emailContent}`,
});

// Client-side useChat hook
'use client';
import { useChat } from 'ai/react';

function ChatInterface() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
    onError: (error) => toast.error(error.message),
  });
  
  return (
    <div>
      {messages.map(m => (
        <div key={m.id} className={m.role === 'user' ? 'user' : 'assistant'}>
          {m.content}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} disabled={isLoading} />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

---

## 10. Observability & Production Architecture

### OpenTelemetry Instrumentation

```typescript
// instrumentation.ts (Next.js)
import { NodeSDK } from '@opentelemetry/sdk-node';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-http';
import { Resource } from '@opentelemetry/resources';
import { SEMRESATTRS_SERVICE_NAME } from '@opentelemetry/semantic-conventions';
import { PrismaInstrumentation } from '@prisma/instrumentation';
import { HttpInstrumentation } from '@opentelemetry/instrumentation-http';

const sdk = new NodeSDK({
  resource: new Resource({
    [SEMRESATTRS_SERVICE_NAME]: process.env.OTEL_SERVICE_NAME || 'my-app',
  }),
  traceExporter: new OTLPTraceExporter({
    url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT,
  }),
  instrumentations: [
    new HttpInstrumentation(),
    new PrismaInstrumentation(),
  ],
});

sdk.start();
```

### Infrastructure (Deployment Decision Guide)

| Scenario | Recommended Platform | Key Reason |
|---|---|---|
| Next.js app, team prefers zero-config | **Vercel** | Native Next.js; Edge Network; instant rollbacks |
| Need full VM control, persistent processes | **Fly.io** | Global Anycast; persistent volumes; WebSockets |
| Simpler deployments, lower cost | **Railway** | Auto-deploys from GitHub; built-in databases |
| Docker-first, multiple services | **Render** | Docker-native; free SSL; managed databases |
| Custom infra, IaC-first | **AWS/GCP + Terraform** | Maximum control; any scale |
| Multi-tenant SaaS, strict data residency | **AWS + RDS** | Compliance; regional data control |

### Security: OWASP Top 10 2025 Mitigations

| Vulnerability | Mitigation in Full-Stack Apps |
|---|---|
| **A01 Broken Access Control** | Server-side auth checks on every request; never trust client; RBAC |
| **A02 Cryptographic Failures** | TLS 1.2+ everywhere; AES-256 at rest; no MD5/SHA1 for passwords (use Argon2) |
| **A03 Injection** | Parameterized queries (ORM enforces); input validation (Zod); output encoding |
| **A04 Insecure Design** | Threat modeling; least privilege; defense in depth |
| **A05 Security Misconfiguration** | SAST in CI; Helmet.js headers; no default credentials; secrets in env vars |
| **A06 Vulnerable Components** | `npm audit` in CI; Dependabot; Snyk; pin versions; SBOM |
| **A07 Auth Failures** | MFA; rate limiting login; secure session management; short session TTL |
| **A08 Integrity Failures** | SRI hashes for CDN assets; code signing; CI/CD pipeline security |
| **A09 Logging/Monitoring** | Structured logs; alerting on anomalies; retain logs 90+ days |
| **A10 SSRF** | Allowlist external URLs; validate redirects; network egress controls |

### Production Architecture Summary

```
Client (Next.js App)
  ↕ HTTPS
CDN Layer (Vercel Edge / CloudFront)
  ↕ Cache miss → origin
API Layer (Next.js API Routes / Hono on Fly.io)
  ↕ tRPC / REST
  ├── Auth: Better Auth (JWT sessions)
  ├── Cache: Redis (Upstash / Fly Redis)
  ├── Queue: BullMQ / Inngest (background jobs)
  └── Database Layer
       ├── PostgreSQL (primary, Neon / Fly.io managed)
       ├── pgvector extension (embeddings)
       └── Read replica (analytics queries)

Observability: OpenTelemetry → Grafana Cloud / Datadog
Monitoring: Sentry (errors) + Vercel Analytics (vitals)
Secrets: Infisical / Doppler → environment variables
CI/CD: GitHub Actions → Preview deployments → Production
```
