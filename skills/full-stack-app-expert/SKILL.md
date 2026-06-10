---
name: full-stack-app-expert
description: Use this skill when you need expert-level knowledge about building modern full-stack web applications. Triggers on questions about: React 19 (Compiler, Server Components, Actions, use hook, Suspense), Next.js 15 (App Router, Partial Prerendering, Server Actions, caching model), TypeScript best practices, Tailwind CSS v4 (@theme directive), shadcn/ui component system, API design (REST vs GraphQL vs tRPC), backend frameworks (Hono, Fastify, Express comparison), database design (PostgreSQL, pgvector, Prisma v6 vs Drizzle ORM), authentication (Auth.js v5, Clerk, Supabase Auth), deployment (Vercel, Railway, Fly.io, Cloudflare Workers), AI integration (Vercel AI SDK, streaming, tool use), testing (Vitest, Testing Library, Playwright), Core Web Vitals (LCP, CLS, INP), monorepo setup (Turborepo vs Nx), security (OWASP Top 10), and edge computing patterns.
---

# Full Stack App Expert

## Frontend: React 19

### React 19 Major Features

**React Compiler (Babel/Vite plugin):**
The most significant change in React 19. Automatically applies memoization transformations — eliminates need for manual `useMemo`, `useCallback`, and `React.memo` in most cases.

```jsx
// Before React Compiler: Manual memoization
const ExpensiveComponent = React.memo(({ data }) => {
  const processed = useMemo(() => process(data), [data]);
  const handler = useCallback(() => doSomething(data), [data]);
  return <div onClick={handler}>{processed}</div>;
});

// With React Compiler: Write naturally, compiler handles it
function ExpensiveComponent({ data }) {
  const processed = process(data);  // Compiler memoizes automatically
  function handler() { doSomething(data); }  // Compiler memoizes
  return <div onClick={handler}>{processed}</div>;
}
```

**Setup:**
```bash
npm install babel-plugin-react-compiler
# or for Next.js:
# next.config.js: experimental: { reactCompiler: true }
```

**React Server Components (RSC):**
Components that run on the server. No client-side JavaScript, can access server resources directly.

```tsx
// app/users/page.tsx — Server Component (default in Next.js App Router)
async function UsersPage() {
  // Direct database query — no API route needed
  const users = await db.select().from(usersTable).orderBy(desc(usersTable.createdAt));
  
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => <UserCard key={user.id} user={user} />)}
    </div>
  );
}
```

**Server Actions:**
Functions that run on the server, called directly from client components. Replaces form submissions and many API route patterns.

```tsx
// Server Action
"use server";
async function createUser(formData: FormData) {
  const name = formData.get("name") as string;
  const email = formData.get("email") as string;
  
  await db.insert(usersTable).values({ name, email });
  revalidatePath("/users");
}

// Client Component using Server Action
"use client";
function CreateUserForm() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button type="submit">Create User</button>
    </form>
  );
}
```

**`use` Hook (React 19):**
New primitive to use promises and contexts inside render functions.

```tsx
import { use, Suspense } from "react";

// Await a promise in render
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);  // Suspense-aware
  return <div>{user.name}</div>;
}

// Use in parent with Suspense
function Page() {
  const userPromise = fetchUser(userId);  // Start fetch, don't await
  return (
    <Suspense fallback={<Skeleton />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

**useFormStatus and useActionState (React 19):**
```tsx
"use client";
import { useActionState } from "react";
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>{pending ? "Saving..." : "Save"}</button>;
}

function Form() {
  const [state, formAction] = useActionState(createUser, { error: null });
  return (
    <form action={formAction}>
      {state.error && <p className="text-red-500">{state.error}</p>}
      <input name="email" type="email" />
      <SubmitButton />
    </form>
  );
}
```

### React Performance Patterns

**Suspense boundaries:** Wrap any async data-fetching component. Shows fallback while loading.

```tsx
<Suspense fallback={<DashboardSkeleton />}>
  <Dashboard />  {/* Can be Server Component that fetches data */}
</Suspense>
```

**Streaming with Suspense:** In Next.js, Server Components inside Suspense boundaries stream independently — user sees content progressively.

**Concurrent rendering:** React 19 defaults to concurrent mode. `startTransition` for non-urgent state updates (keeps UI responsive during heavy updates).

```tsx
import { startTransition, useState } from "react";

function SearchInput() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  
  function handleChange(e) {
    setQuery(e.target.value);  // Urgent: update input immediately
    startTransition(() => {
      setResults(search(e.target.value));  // Non-urgent: can be interrupted
    });
  }
  
  return <input value={query} onChange={handleChange} />;
}
```

---

## Next.js 15

### App Router Architecture

**File conventions:**
```
app/
├── layout.tsx          # Root layout (persistent shell)
├── page.tsx            # / route
├── loading.tsx         # Loading UI (Suspense fallback)
├── error.tsx           # Error boundary (client component)
├── not-found.tsx       # 404 page
├── (marketing)/        # Route group (no URL segment)
│   ├── about/page.tsx  # /about
│   └── pricing/page.tsx # /pricing
├── dashboard/
│   ├── layout.tsx      # Nested layout
│   ├── page.tsx        # /dashboard
│   └── [id]/           # Dynamic segment
│       └── page.tsx    # /dashboard/[id]
└── api/
    └── users/
        └── route.ts    # /api/users (API route)
```

**Component types:**
- `page.tsx`, `layout.tsx`, most components → Server Components (default)
- Add `"use client"` for interactivity, browser APIs, event listeners, useState/useEffect

### Caching in Next.js 15

**Next.js 15 changes from 14:** Caching is now **opt-in** (was opt-out). Fetch requests and Route Handlers are NOT cached by default.

```tsx
// Not cached (Next.js 15 default)
const data = await fetch("https://api.example.com/data");

// Cache indefinitely (like old default)
const data = await fetch("https://api.example.com/data", {
  cache: "force-cache"
});

// Revalidate every 60 seconds
const data = await fetch("https://api.example.com/data", {
  next: { revalidate: 60 }
});

// Never cache (dynamic)
const data = await fetch("https://api.example.com/data", {
  cache: "no-store"
});
```

**Revalidation:**
```tsx
import { revalidatePath, revalidateTag } from "next/cache";

// After mutation, revalidate specific path
"use server";
async function updateUser(id: string, data: UserUpdate) {
  await db.update(usersTable).set(data).where(eq(usersTable.id, id));
  revalidatePath("/users");           // Revalidate path
  revalidatePath(`/users/${id}`);     // Revalidate dynamic path
  revalidateTag("users");             // Revalidate by tag
}
```

### Partial Prerendering (PPR)

Next.js 15's experimental feature. Pre-render static shell at build time, stream dynamic content at request time.

```tsx
// next.config.ts
export default {
  experimental: { ppr: true }
};

// Page with static shell + dynamic parts
export default function Page() {
  return (
    <>
      <StaticHeader />     {/* Pre-rendered at build time */}
      <StaticHero />       {/* Pre-rendered at build time */}
      <Suspense fallback={<CartSkeleton />}>
        <ShoppingCart />   {/* Streamed dynamically (reads cookies) */}
      </Suspense>
      <Suspense fallback={<RecommendationsSkeleton />}>
        <Recommendations /> {/* Streamed dynamically */}
      </Suspense>
    </>
  );
}
```

### API Routes

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const limit = parseInt(searchParams.get("limit") ?? "10");
  
  const users = await db.select().from(usersTable).limit(limit);
  return NextResponse.json(users);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  const validated = CreateUserSchema.parse(body);  // Zod validation
  
  const [user] = await db.insert(usersTable).values(validated).returning();
  return NextResponse.json(user, { status: 201 });
}

// Dynamic route: app/api/users/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  const user = await db.select().from(usersTable)
    .where(eq(usersTable.id, params.id))
    .limit(1);
  
  if (!user.length) {
    return NextResponse.json({ error: "Not found" }, { status: 404 });
  }
  return NextResponse.json(user[0]);
}
```

---

## UI: Tailwind CSS v4 and shadcn/ui

### Tailwind CSS v4

**Major change:** Theme configuration moves from `tailwind.config.js` to CSS using `@theme` directive.

```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  /* Custom colors */
  --color-brand-50: #eff6ff;
  --color-brand-500: #3b82f6;
  --color-brand-900: #1e3a8a;
  
  /* Custom spacing */
  --spacing-18: 4.5rem;
  
  /* Custom fonts */
  --font-display: "Cal Sans", sans-serif;
  
  /* Custom breakpoints */
  --breakpoint-3xl: 1920px;
}
```

**Using theme variables:**
```html
<div class="bg-brand-500 text-brand-50 font-display pt-18">
  Content
</div>
```

**Performance:** v4 builds 5x faster than v3, uses CSS cascade layers, generates smaller output via `@layer` and lightning-css.

### shadcn/ui

Not a library — it's a code generator. Components are copied into your project, fully owned and customizable.

```bash
# Initialize
npx shadcn@latest init

# Add components (copies into components/ui/)
npx shadcn@latest add button card dialog form input table toast
```

**Why shadcn/ui wins:**
- Full ownership (no dependency version hell)
- Tailwind-based → easy customization
- Radix UI primitives underneath → accessibility built-in
- TypeScript-first
- Variants via CVA (Class Variance Authority)

**Custom variants with CVA:**
```tsx
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md font-medium transition-colors",
  {
    variants: {
      variant: {
        default: "bg-brand-500 text-white hover:bg-brand-600",
        outline: "border border-brand-500 text-brand-500 hover:bg-brand-50",
        ghost: "hover:bg-brand-50 text-brand-600",
      },
      size: {
        sm: "h-8 px-3 text-xs",
        md: "h-10 px-4 text-sm",
        lg: "h-12 px-6 text-base",
      },
    },
    defaultVariants: { variant: "default", size: "md" },
  }
);
```

---

## Backend and APIs

### API Design: REST vs. GraphQL vs. tRPC

**REST (Most Common):**
- Resource-based URLs, HTTP verbs (GET/POST/PUT/PATCH/DELETE)
- Standard, broad tooling, easy caching
- Over-fetching: GET /users returns all fields when you need just name
- Under-fetching: Need multiple requests for related data

**GraphQL:**
- Query exactly the fields you need
- Single endpoint (`/graphql`)
- Subscriptions for real-time
- Overhead: Schema definition, N+1 problems (use DataLoader), complexity
- Best for: Public APIs with diverse clients, complex data graphs

**tRPC (Growing — 15% adoption among TypeScript devs in 2025):**
- End-to-end type safety: Procedure defined on server, autocompleted on client
- No code generation step (unlike GraphQL)
- Works seamlessly with Next.js
- Best for: TypeScript-only full-stack monorepos

```typescript
// tRPC server (server/trpc.ts)
const appRouter = router({
  users: router({
    list: publicProcedure
      .input(z.object({ limit: z.number().default(10) }))
      .query(async ({ input }) => {
        return db.select().from(usersTable).limit(input.limit);
      }),
    create: protectedProcedure
      .input(CreateUserSchema)
      .mutation(async ({ input, ctx }) => {
        return db.insert(usersTable).values({ ...input, createdBy: ctx.user.id }).returning();
      }),
  }),
});

// Client usage — fully typed, autocompleted
const { data: users } = trpc.users.list.useQuery({ limit: 20 });
const createUser = trpc.users.create.useMutation();
await createUser.mutateAsync({ name: "John", email: "john@example.com" });
```

### Backend Frameworks Comparison

| Framework | Throughput | Bundle Size | DX | Ecosystem |
|-----------|-----------|-------------|-----|-----------|
| Hono | 55-65K req/s | ~14KB | Excellent | Growing rapidly |
| Fastify | 50-60K req/s | ~83KB | Very good | Mature |
| Express | 15-25K req/s | ~208KB | Good | Largest |
| Elysia (Bun) | 90K+ req/s | Tiny | Excellent | New |

**Hono (recommended for new projects):**
```typescript
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { bearerAuth } from "hono/bearer-auth";

const app = new Hono();

// Middleware
app.use("/api/*", bearerAuth({ token: process.env.API_TOKEN! }));

// Route with validation
app.post(
  "/api/users",
  zValidator("json", CreateUserSchema),
  async (c) => {
    const body = c.req.valid("json");
    const user = await createUser(body);
    return c.json(user, 201);
  }
);

// Runs on Node, Bun, Deno, Cloudflare Workers (same code)
export default app;
```

---

## Database Layer

### PostgreSQL with pgvector

**pgvector extension:** Vector similarity search inside PostgreSQL — no separate vector database needed for most use cases.

```sql
-- Enable extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Table with embedding column
CREATE TABLE documents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content TEXT NOT NULL,
  embedding vector(1536),  -- OpenAI text-embedding-3-small dimensions
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- IVFFlat index for approximate search (faster, less accurate)
CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops)
  WITH (lists = 100);  -- sqrt(row_count) lists as rule of thumb

-- HNSW index (more accurate, higher memory, no training needed)
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops)
  WITH (m = 16, ef_construction = 64);
```

```typescript
// Drizzle ORM with pgvector
import { pgTable, uuid, text, jsonb } from "drizzle-orm/pg-core";
import { vector } from "drizzle-orm/pg-core";

const documents = pgTable("documents", {
  id: uuid("id").primaryKey().defaultRandom(),
  content: text("content").notNull(),
  embedding: vector("embedding", { dimensions: 1536 }),
  metadata: jsonb("metadata"),
});

// Similarity search
const results = await db
  .select()
  .from(documents)
  .orderBy(sql`embedding <=> ${queryEmbedding}`)  // cosine distance
  .limit(10);
```

### Prisma v6 vs. Drizzle ORM

**Prisma v6:**
```typescript
// schema.prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

// Usage — fully type-safe, intuitive API
const users = await prisma.user.findMany({
  where: { email: { contains: "@example.com" } },
  include: { posts: true },
  orderBy: { createdAt: "desc" },
  take: 10,
});
```

**Drizzle ORM (newer, faster):**
```typescript
// schema.ts (TypeScript-first, no separate schema file)
const users = pgTable("users", {
  id: text("id").primaryKey().$defaultFn(() => createId()),
  email: text("email").notNull().unique(),
  name: text("name"),
  createdAt: timestamp("created_at").defaultNow(),
});

// Usage — SQL-like, more explicit
const result = await db
  .select()
  .from(users)
  .where(like(users.email, "%@example.com%"))
  .orderBy(desc(users.createdAt))
  .limit(10);
```

**When to choose:**
- Prisma: Better DX for teams new to ORM, strong migration tooling, Prisma Studio UI
- Drizzle: Better performance (~40% faster queries), smaller bundle, SQL familiarity, edge runtime compatible

---

## Authentication

### Auth.js v5 (NextAuth)

```typescript
// auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import Google from "next-auth/providers/google";
import { DrizzleAdapter } from "@auth/drizzle-adapter";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: DrizzleAdapter(db),
  providers: [
    GitHub({
      clientId: process.env.AUTH_GITHUB_ID,
      clientSecret: process.env.AUTH_GITHUB_SECRET,
    }),
    Google({
      clientId: process.env.AUTH_GOOGLE_ID,
      clientSecret: process.env.AUTH_GOOGLE_SECRET,
    }),
  ],
  callbacks: {
    session({ session, token }) {
      session.user.id = token.sub!;
      return session;
    },
  },
});

// app/api/auth/[...nextauth]/route.ts
export const { GET, POST } = handlers;

// Usage in Server Component
const session = await auth();
if (!session) redirect("/login");
```

### Clerk (Managed Auth)

Best for: Fast setup, built-in UI components, organizations/teams support, B2B SaaS.

```typescript
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server";

const isProtectedRoute = createRouteMatcher(["/dashboard(.*)", "/api(.*)"]);

export default clerkMiddleware(async (auth, req) => {
  if (isProtectedRoute(req)) await auth.protect();
});

// Server Component
import { auth, currentUser } from "@clerk/nextjs/server";

async function DashboardPage() {
  const { userId } = await auth();
  const user = await currentUser();
  
  return <div>Welcome, {user?.firstName}</div>;
}
```

**Auth.js vs. Clerk vs. Supabase Auth:**
| Option | Best For | Cost | Control |
|--------|---------|------|---------|
| Auth.js v5 | Self-hosted, open source, full control | Free | High |
| Clerk | Fastest setup, enterprise features (orgs) | $25+/mo | Medium |
| Supabase Auth | Already using Supabase, RLS integration | Free/usage | Medium |

---

## Deployment and Infrastructure

### Vercel (Recommended for Next.js)

**Features:**
- Zero-config Next.js deployment
- Edge Functions, Serverless Functions, ISR, PPR
- Preview deployments for every PR
- Analytics, Speed Insights, Web Analytics built-in
- KV (Redis), Postgres (Neon), Blob (R2) — managed databases

```bash
# Deploy
npx vercel --prod

# Or via GitHub integration (automatic on push)
```

**Vercel pricing considerations:**
- Hobby: Free (personal projects, limited)
- Pro: $20/user/month (teams, custom domains, higher limits)
- Enterprise: Custom (SSO, SLA, dedicated support)
- Database: Separate usage-based pricing

### Railway

Best for: Backend services, full-stack without Next.js, PostgreSQL, Redis, custom Docker.

```bash
# Install CLI and deploy
npm install -g @railway/cli
railway login
railway init
railway up
```

**Railway strengths:**
- Simple pricing ($5/user/month + usage)
- PostgreSQL with automated backups
- Private networking between services
- Environment variable management
- GitHub auto-deploy

### Fly.io

Best for: Global deployment, WebSockets, long-running processes, Elixir/Phoenix.

```bash
# Deploy Docker app
fly launch    # Detects app type, creates fly.toml
fly deploy    # Build and push to Fly infrastructure
fly scale count 3 --region sin,lax,iad  # Multi-region
```

### Cloudflare Workers

**Edge runtime:** Runs at 300+ edge locations, cold start <5ms (vs 200ms+ for serverless).

```typescript
// worker.ts
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    if (url.pathname === "/api/data") {
      const data = await env.KV.get("key");  // Workers KV
      return Response.json({ data });
    }
    
    return new Response("Not found", { status: 404 });
  },
};
```

**When to use Workers:**
- Global low-latency API
- Authentication/authorization middleware
- A/B testing, feature flags
- Image optimization, caching
- Lightweight serverless (no Node.js APIs needed)

---

## AI Integration with Vercel AI SDK

### Streaming Text Generation

```typescript
import { streamText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";

// Next.js Route Handler
export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    system: "You are a helpful assistant.",
    messages,
  });
  
  return result.toDataStreamResponse();
}
```

```tsx
// Client component
"use client";
import { useChat } from "ai/react";

export function ChatInterface() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat",
  });
  
  return (
    <div>
      {messages.map(m => (
        <div key={m.id} className={m.role === "user" ? "text-right" : "text-left"}>
          {m.content}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} disabled={isLoading} />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

### Tool Use with AI SDK

```typescript
import { streamText, tool } from "ai";
import { z } from "zod";

const result = streamText({
  model: anthropic("claude-sonnet-4-6"),
  tools: {
    searchDatabase: tool({
      description: "Search the product database",
      parameters: z.object({
        query: z.string().describe("Search query"),
        category: z.enum(["electronics", "clothing", "food"]).optional(),
      }),
      execute: async ({ query, category }) => {
        return await db.products.search(query, { category });
      },
    }),
    getOrderStatus: tool({
      description: "Get order status by order ID",
      parameters: z.object({ orderId: z.string() }),
      execute: async ({ orderId }) => {
        return await db.orders.get(orderId);
      },
    }),
  },
  maxSteps: 5,  // Allow up to 5 tool call rounds
  messages,
});
```

---

## Testing Strategy

### Testing Pyramid

**Unit tests (60-70% of tests):**
- Test pure functions, utilities, validators
- Fast, isolated, no external dependencies
- Tool: Vitest

**Integration tests (20-30%):**
- Test API routes, database operations, service functions
- May use test database, in-memory implementations
- Tool: Vitest + test database

**E2E tests (10-20%):**
- Critical user journeys (signup, checkout, key workflows)
- Runs against real or staging environment
- Tool: Playwright

```typescript
// Vitest unit test
import { describe, it, expect, vi } from "vitest";
import { formatCurrency } from "../lib/formatters";

describe("formatCurrency", () => {
  it("formats USD correctly", () => {
    expect(formatCurrency(1234.56, "USD")).toBe("$1,234.56");
  });
  
  it("handles zero", () => {
    expect(formatCurrency(0, "USD")).toBe("$0.00");
  });
});

// React Testing Library
import { render, screen, userEvent } from "@testing-library/react";
import { LoginForm } from "../components/LoginForm";

it("submits form with email and password", async () => {
  const onSubmit = vi.fn();
  render(<LoginForm onSubmit={onSubmit} />);
  
  await userEvent.type(screen.getByLabelText("Email"), "test@example.com");
  await userEvent.type(screen.getByLabelText("Password"), "password123");
  await userEvent.click(screen.getByRole("button", { name: "Sign in" }));
  
  expect(onSubmit).toHaveBeenCalledWith({
    email: "test@example.com",
    password: "password123",
  });
});
```

```typescript
// Playwright E2E test
import { test, expect } from "@playwright/test";

test("user can sign up and view dashboard", async ({ page }) => {
  await page.goto("/");
  await page.click("text=Get started");
  
  await page.fill('[name="email"]', "newuser@example.com");
  await page.fill('[name="password"]', "SecurePass123!");
  await page.click('[type="submit"]');
  
  await expect(page).toHaveURL("/dashboard");
  await expect(page.locator("h1")).toContainText("Welcome");
});
```

---

## Core Web Vitals (2025)

### Current Thresholds

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP (Largest Contentful Paint) | ≤2.5s | 2.5-4.0s | >4.0s |
| INP (Interaction to Next Paint) | ≤200ms | 200-500ms | >500ms |
| CLS (Cumulative Layout Shift) | ≤0.1 | 0.1-0.25 | >0.25 |

**Note:** INP replaced FID (First Input Delay) as a Core Web Vital in March 2024.

**LCP optimization:**
- Preload hero images: `<link rel="preload" as="image" href="hero.webp">`
- Use `fetchpriority="high"` on hero `<img>`
- Serve images in WebP/AVIF format (30-50% smaller)
- Next.js Image component handles most of this automatically

**INP optimization:**
- Break long tasks (>50ms) with `scheduler.yield()`
- Use web workers for heavy computation
- React Compiler automatically reduces unnecessary re-renders

**CLS optimization:**
- Always set width/height on images (or use aspect-ratio)
- Reserve space for ads and dynamic content
- Avoid inserting content above existing content

---

## Security: OWASP Top 10 for Web Apps

### Critical Vulnerabilities to Prevent

**A01: Broken Access Control**
```typescript
// Always verify user owns the resource
async function getOrder(orderId: string, userId: string) {
  const order = await db.orders.findFirst({
    where: {
      id: orderId,
      userId: userId,  // Verify ownership — never just use orderId
    },
  });
  if (!order) throw new Error("Not found");
  return order;
}
```

**A03: SQL Injection**
```typescript
// Never: string interpolation
const results = await db.execute(`SELECT * FROM users WHERE email = '${email}'`);

// Always: parameterized (ORM handles this)
const user = await db.select().from(users).where(eq(users.email, email));
```

**A07: Cross-Site Scripting (XSS)**
- React auto-escapes JSX expressions → safe by default
- Never use `dangerouslySetInnerHTML` with user input
- Set `Content-Security-Policy` headers

**A02: Cryptographic Failures**
```typescript
import { hash, compare } from "bcrypt";

// Hash passwords
const hashed = await hash(password, 12);  // salt rounds: 10-14

// Never store plain passwords, never compare directly
const valid = await compare(inputPassword, storedHash);
```

**A08: Security Misconfiguration (Next.js security headers):**
```typescript
// next.config.ts
const headers = [
  { key: "X-Frame-Options", value: "DENY" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  {
    key: "Content-Security-Policy",
    value: [
      "default-src 'self'",
      "script-src 'self' 'nonce-{nonce}'",
      "style-src 'self' 'unsafe-inline'",
      "img-src 'self' data: https:",
    ].join("; ")
  },
];
```

---

## Monorepo: Turborepo vs. Nx

### When to Use a Monorepo
- Multiple apps sharing code (web + mobile + API)
- Shared UI component library
- Multiple microservices sharing types/utilities
- Team of 3+ people, 2+ deployable units

### Turborepo (Simpler)

```json
// turbo.json
{
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
      "dependsOn": ["^build"]
    },
    "lint": {}
  }
}
```

**Structure:**
```
apps/
  web/          # Next.js app
  api/          # Hono API
  mobile/       # React Native
packages/
  ui/           # Shared shadcn/ui components
  db/           # Shared database schema (Drizzle)
  auth/         # Shared auth utilities
  types/        # Shared TypeScript types
```

**Turborepo advantages:** Simple config, fast remote caching (Vercel), great Next.js integration, lower learning curve.

**Nx advantages:** More powerful (code generators, custom plugins), better for large teams, supports more frameworks/languages, detailed dependency graph visualization.

**Rule:** Use Turborepo for TypeScript/JavaScript monorepos. Consider Nx when you have >10 packages, non-JavaScript projects, or need custom generators.
