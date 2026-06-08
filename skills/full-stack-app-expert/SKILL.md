---
name: full-stack-app-expert
description: Expert-level full-stack application developer for the 2025-2026 stack. Covers React 19 (useActionState, useOptimistic, use(), Server Components, Server Actions), Next.js 16 (Turbopack stable, View Transitions, AI scaffolding), Svelte 5 Runes, TypeScript 5.x (isolatedDeclarations, Node.js native TS stripping), tRPC v11, Hono for edge/serverless, Drizzle ORM v1 vs Prisma 7 (Rust binary removed Nov 2025), Neon/PlanetScale/Supabase database choices, Better Auth v1 (Auth.js successor), passkeys/WebAuthn (1B+ activations), Vercel AI SDK 5 (agentic, MCP integration), Turborepo monorepo, pnpm workspaces, Vitest + RTL + MSW + Playwright testing, Core Web Vitals (INP replaces FID), and deployment (Vercel/Cloudflare Workers/Fly.io). Use when architecting, building, debugging, or reviewing full-stack TypeScript applications.
---

# Full-Stack App Expert

You are a senior full-stack engineer with deep expertise in the complete TypeScript application stack as of 2025-2026. You know the specific library versions, breaking changes, correct APIs, and architectural patterns that produce maintainable, performant production applications.

---

## CORE PRINCIPLES

1. **Server-first rendering**: Start with RSC/SSR; add client interactivity only where needed
2. **Type safety end-to-end**: TypeScript from DB schema to UI props eliminates entire categories of bugs
3. **Auth is load-bearing**: A broken auth system is an existential threat; use battle-tested libraries
4. **Measure what you ship**: Core Web Vitals (INP, LCP, CLS) are ranking signals and user experience
5. **Test the behavior, not the implementation**: Integration tests > unit tests for UI; e2e for critical paths

---

## 1. REACT 19 (Current Stable: 19.x, 2025-2026)

### useActionState (Replaces useFormState)

`useFormState` was renamed to `useActionState` in React 19 and moved to `react` from `react-dom`.

```typescript
"use client";
import { useActionState } from "react";

type FormState = { error?: string; success?: boolean };

async function createPost(prevState: FormState, formData: FormData): Promise<FormState> {
  "use server";
  const title = formData.get("title") as string;
  if (!title?.trim()) return { error: "Title is required" };
  
  await db.post.create({ data: { title } });
  return { success: true };
}

export function PostForm() {
  const [state, formAction, isPending] = useActionState(createPost, {});
  
  return (
    <form action={formAction}>
      <input name="title" disabled={isPending} />
      {state.error && <p className="text-red-500">{state.error}</p>}
      {state.success && <p className="text-green-500">Post created!</p>}
      <button type="submit" disabled={isPending}>
        {isPending ? "Creating..." : "Create Post"}
      </button>
    </form>
  );
}
```

### useOptimistic

```typescript
"use client";
import { useOptimistic, useTransition } from "react";

type Todo = { id: string; text: string; completed: boolean };

export function TodoList({ initialTodos }: { initialTodos: Todo[] }) {
  const [todos, setTodos] = useOptimistic(
    initialTodos,
    (state: Todo[], updatedTodo: Todo) =>
      state.map(t => t.id === updatedTodo.id ? updatedTodo : t)
  );
  
  const [, startTransition] = useTransition();
  
  async function toggleTodo(todo: Todo) {
    const optimistic = { ...todo, completed: !todo.completed };
    startTransition(async () => {
      setTodos(optimistic);  // Immediate optimistic update
      await updateTodoInDb(optimistic);  // Actual async update
    });
  }
  
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id} onClick={() => toggleTodo(todo)}
            style={{ opacity: todo.completed ? 0.5 : 1 }}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}
```

### use() — Data Fetching in Components

```typescript
// use() unwraps promises and Context in render
import { use, Suspense } from "react";

// Works with promises passed as props
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);  // Suspends until resolved
  return <div>{user.name}</div>;
}

// Works with Context
function ThemedButton() {
  const theme = use(ThemeContext);  // More flexible than useContext
  return <button className={theme.button}>Click</button>;
}

// Usage: pass promise down (server component pattern)
async function Page({ params }: { params: { id: string } }) {
  const userPromise = fetchUser(params.id);  // Don't await — pass promise
  return (
    <Suspense fallback={<Skeleton />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

### React Server Components (RSC) Architecture

```typescript
// Server Component (default in Next.js app dir — no "use client")
async function ProductPage({ id }: { id: string }) {
  // Can fetch directly from DB — no API route needed
  const product = await db.product.findUnique({ where: { id } });
  if (!product) notFound();
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      {/* Pass serializable data to client component */}
      <AddToCartButton productId={product.id} price={product.price} />
    </div>
  );
}

// Client Component — needed for interactivity
"use client";
import { useState } from "react";

function AddToCartButton({ productId, price }: { productId: string; price: number }) {
  const [quantity, setQuantity] = useState(1);
  return (
    <div>
      <input type="number" value={quantity} onChange={e => setQuantity(+e.target.value)} min={1} />
      <button onClick={() => addToCart(productId, quantity)}>
        Add to Cart (${(price * quantity / 100).toFixed(2)})
      </button>
    </div>
  );
}
```

---

## 2. NEXT.JS 16 (Current: 16.x, 2025-2026)

### Breaking Changes from Next.js 15 (Know Before Upgrading)

**Caching defaults reversed (Next.js 15 change that carries forward):**
- `fetch()` is NO LONGER cached by default — you must opt in explicitly
- Dynamic routes are NOT cached by default
- `cookies()` and `headers()` are no longer automatically dynamic boundaries

```typescript
// Next.js 16: Explicit cache control
export default async function Page() {
  // Cached indefinitely (equivalent to old default)
  const data = await fetch("/api/static", { cache: "force-cache" });
  
  // Cached for 60 seconds
  const semiStatic = await fetch("/api/data", { next: { revalidate: 60 } });
  
  // Never cached (was the exception; now more common)
  const live = await fetch("/api/realtime", { cache: "no-store" });
}
```

**Turbopack: Stable in Next.js 15+, Default in 16**
Turbopack replaces webpack as the default bundler. You no longer need `--turbopack` flag.

```bash
# Automatically uses Turbopack in Next.js 16
next dev
next build

# Performance improvement: 3-10x faster hot reload vs. webpack
# Build time improvement: 2-5x for incremental builds
# Note: Some webpack plugins have no Turbopack equivalent — check compatibility
```

### App Router Best Practices

```
/app
  /layout.tsx          ← Root layout (always server)
  /page.tsx            ← Home page
  /(marketing)         ← Route group (no URL segment)
    /about/page.tsx
    /pricing/page.tsx
  /(app)               ← Route group requiring auth
    /layout.tsx        ← Auth check layout
    /dashboard/page.tsx
  /api
    /webhooks
      /stripe/route.ts ← Route handler
  /[slug]
    /page.tsx          ← Dynamic route
    /loading.tsx       ← Suspense boundary
    /error.tsx         ← Error boundary
    /not-found.tsx     ← 404 handler
```

### View Transitions API (Next.js 16)

```typescript
// Enable cross-page transitions
import { unstable_ViewTransition as ViewTransition } from "next/navigation";

export default function Layout({ children }) {
  return (
    <ViewTransition>
      {children}
    </ViewTransition>
  );
}

// Animate specific elements with view-transition-name
<div style={{ viewTransitionName: `post-${post.id}` }}>
  {post.title}
</div>
```

### Server Actions

```typescript
// app/actions.ts
"use server";
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";
import { z } from "zod";

const CreatePostSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(10),
});

export async function createPost(formData: FormData) {
  const session = await getServerSession();
  if (!session) throw new Error("Unauthorized");
  
  const validated = CreatePostSchema.parse({
    title: formData.get("title"),
    content: formData.get("content"),
  });
  
  const post = await db.post.create({
    data: { ...validated, authorId: session.user.id }
  });
  
  revalidatePath("/posts");
  redirect(`/posts/${post.id}`);
}
```

---

## 3. TYPESCRIPT 5.X (2025-2026)

### isolatedDeclarations (TypeScript 5.5+)
Forces type annotations on exported declarations, enabling parallel type checking and declaration emit.

```typescript
// With isolatedDeclarations: true, this errors:
export function greet(name: string) {  // ❌ Return type must be explicit
  return `Hello, ${name}`;
}

// Fix:
export function greet(name: string): string {  // ✓
  return `Hello, ${name}`;
}
```

Enable in tsconfig.json: `"isolatedDeclarations": true`
**Why**: Enables tools like oxc and esbuild to emit .d.ts files in parallel without full type-checking, speeding up large monorepos.

### Node.js 24+ Native TypeScript Support
Node.js 24 strips TypeScript type annotations natively (no transpilation, but also no type checking):

```bash
# Run TypeScript files directly in Node.js 24+
node --experimental-strip-types server.ts

# Also available as stable in Node.js 24.x:
node server.ts  # (if package.json type:module + .ts extension)
```

**Limitations**: Decorators and const enums still require transpilation. Use tsx or ts-node for those.

### Template Literal Types Patterns

```typescript
type EventName = "click" | "hover" | "focus";
type Handler = `on${Capitalize<EventName>}`;
// type Handler = "onClick" | "onHover" | "onFocus"

type Column = "id" | "name" | "email";
type SortableColumn = `${Column}:${"asc" | "desc"}`;
// type SortableColumn = "id:asc" | "id:desc" | "name:asc" | ...

// Real-world: type-safe event emitter
type Listener<T extends object> = {
  [K in keyof T as `on${Capitalize<string & K>}`]: (value: T[K]) => void;
};
```

---

## 4. TRPC v11 + HONO

### tRPC v11 — Type-Safe API Layer

```typescript
// server/trpc.ts
import { initTRPC, TRPCError } from "@trpc/server";
import { z } from "zod";

const t = initTRPC.context<{ userId: string | null }>().create();

const isAuthed = t.middleware(({ ctx, next }) => {
  if (!ctx.userId) throw new TRPCError({ code: "UNAUTHORIZED" });
  return next({ ctx: { userId: ctx.userId } });  // userId narrowed to string
});

const publicProcedure = t.procedure;
const protectedProcedure = t.procedure.use(isAuthed);

export const appRouter = t.router({
  post: t.router({
    list: publicProcedure
      .input(z.object({ cursor: z.string().optional(), limit: z.number().default(20) }))
      .query(async ({ input }) => {
        const posts = await db.post.findMany({
          take: input.limit,
          cursor: input.cursor ? { id: input.cursor } : undefined,
          orderBy: { createdAt: "desc" }
        });
        return { posts, nextCursor: posts[posts.length - 1]?.id };
      }),
    
    create: protectedProcedure
      .input(z.object({ title: z.string().min(1), content: z.string() }))
      .mutation(async ({ input, ctx }) => {
        return db.post.create({ data: { ...input, authorId: ctx.userId } });
      }),
  })
});

export type AppRouter = typeof appRouter;
```

```typescript
// Client usage
import { createTRPCReact } from "@trpc/react-query";
import type { AppRouter } from "@/server/trpc";

const trpc = createTRPCReact<AppRouter>();

function PostList() {
  const { data, isLoading } = trpc.post.list.useQuery({ limit: 10 });
  const createPost = trpc.post.create.useMutation({
    onSuccess: () => trpc.post.list.invalidate()
  });
  
  if (isLoading) return <Skeleton />;
  return (
    <>
      {data?.posts.map(p => <PostCard key={p.id} post={p} />)}
      <button onClick={() => createPost.mutate({ title: "New Post", content: "..." })}>
        Create
      </button>
    </>
  );
}
```

### Hono — Edge/Serverless API Framework

```typescript
// Fast, lightweight web framework for Cloudflare Workers, Bun, Node.js
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { bearerAuth } from "hono/bearer-auth";
import { z } from "zod";

const app = new Hono();

// Middleware
app.use("/api/*", bearerAuth({ token: process.env.API_TOKEN! }));

app.get("/api/users/:id",
  zValidator("param", z.object({ id: z.string().uuid() })),
  async (c) => {
    const { id } = c.req.valid("param");
    const user = await db.user.findUnique({ where: { id } });
    if (!user) return c.json({ error: "Not found" }, 404);
    return c.json(user);
  }
);

app.post("/api/users",
  zValidator("json", z.object({ email: z.string().email(), name: z.string() })),
  async (c) => {
    const body = c.req.valid("json");
    const user = await db.user.create({ data: body });
    return c.json(user, 201);
  }
);

export default app;
```

**Hono vs. Express benchmark (2026)**: Hono is ~3x faster than Express on Node.js, 10x+ faster in Cloudflare Workers context due to zero-overhead design.

---

## 5. DATABASE AND ORM (2025-2026)

### Drizzle ORM v1 vs. Prisma 7

**Prisma 7 (November 2025)**
Major change: Rust binary (the query engine) removed. Pure JavaScript/TypeScript implementation.
- 90% smaller package size
- No more native binary compatibility issues (ARM vs. x64)
- Improved Edge runtime compatibility
- **Migration**: `prisma generate` no longer downloads binary; update tsconfig/bundler excludes accordingly

```typescript
// Prisma 7 - same API, faster startup, smaller bundle
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient({
  datasources: { db: { url: process.env.DATABASE_URL } },
  log: ["error"],
});

const user = await prisma.user.findUnique({
  where: { email: "user@example.com" },
  include: { posts: { take: 5, orderBy: { createdAt: "desc" } } }
});
```

**Drizzle ORM v1 (2026)**
Following PlanetScale acquisition of Drizzle Labs (March 2026), Drizzle became the recommended ORM for MySQL-compatible databases. Continued strong growth as lightweight Prisma alternative.

```typescript
// Drizzle ORM v1 - schema definition
import { pgTable, uuid, varchar, text, timestamp, index } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: uuid("id").primaryKey().defaultRandom(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  name: varchar("name", { length: 100 }).notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
}, (table) => [
  index("idx_users_email").on(table.email),
]);

export const posts = pgTable("posts", {
  id: uuid("id").primaryKey().defaultRandom(),
  title: varchar("title", { length: 200 }).notNull(),
  content: text("content"),
  authorId: uuid("author_id").references(() => users.id).notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

// Type-safe queries
import { drizzle } from "drizzle-orm/node-postgres";
import { eq, desc, sql } from "drizzle-orm";

const db = drizzle(pool, { schema: { users, posts } });

const userWithPosts = await db.query.users.findFirst({
  where: eq(users.email, "user@example.com"),
  with: {
    posts: {
      limit: 5,
      orderBy: [desc(posts.createdAt)]
    }
  }
});

// Drizzle advantage: SQL-like syntax, no magic, maximum control
const result = await db
  .select({
    user: users,
    postCount: sql<number>`count(${posts.id})::int`
  })
  .from(users)
  .leftJoin(posts, eq(posts.authorId, users.id))
  .groupBy(users.id);
```

### Database Platform Choices (2026)

**Neon (Acquired by Databricks, announced 2026)**
- Best for: Serverless PostgreSQL; Next.js + Vercel deployments; branching for preview environments
- Key feature: Database branching (create branch per PR — each gets own DB state)
- Price: Free tier 0.5GB; Pro $19/month; usage-based compute
- Post-Databricks: Roadmap expansion to analytics workloads; Lakehouse integration

**Supabase**
- Best for: Teams wanting full BaaS (Postgres + Auth + Storage + Realtime + Edge Functions)
- Differentiator: Open source; can self-host; RLS (Row Level Security) for client-side auth
- Price: Free tier; Pro $25/month/project
- 2026: Supabase AI (pg_net + pgvector + Supabase AI functions built-in)

**PlanetScale (Acquired by Drizzle Labs)**
- Best for: MySQL-compatible; horizontal sharding; large-scale (vitess-based)
- Key feature: Non-blocking schema changes (no table locks on migrations)
- Note: After Drizzle acquisition, tighter Drizzle integration; Prisma support remains

**Turso (libSQL)**
- Best for: Edge SQLite; low-latency reads everywhere; embedded databases
- Use case: Per-tenant isolated databases at edge; offline-first apps

---

## 6. AUTHENTICATION (2025-2026)

### Better Auth v1 (Took Over Auth.js in September 2025)

Auth.js (formerly NextAuth.js) was effectively superseded when the original maintainer launched Better Auth as a rewrite. Lucia Auth deprecated itself in July 2025 (maintainer recommended Better Auth as replacement).

```typescript
// auth.ts - Better Auth setup
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { passkey } from "better-auth/plugins";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: "pg",
    schema: { users, sessions, accounts, verifications }
  }),
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
    minPasswordLength: 12,
  },
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
  plugins: [
    passkey(),  // WebAuthn/passkeys support
    twoFactor({ issuer: "MyApp" }),  // TOTP 2FA
  ],
  session: {
    expiresIn: 60 * 60 * 24 * 30,  // 30 days
    updateAge: 60 * 60 * 24,        // Refresh if > 1 day old
    cookieCache: { enabled: true, maxAge: 5 * 60 }  // Cache session for 5 min
  },
  rateLimit: {
    window: 60,
    max: 10,  // 10 requests per minute to auth endpoints
  }
});
```

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

```typescript
// Server component - get session
import { auth } from "@/auth";
import { headers } from "next/headers";

async function ProtectedPage() {
  const session = await auth.api.getSession({ headers: await headers() });
  if (!session) redirect("/login");
  
  return <div>Hello, {session.user.name}</div>;
}
```

### Passkeys / WebAuthn (2026 Mainstream)
Over 1 billion passkey activations as of early 2026 (Apple, Google, Microsoft all default to passkeys).

```typescript
// Better Auth passkey registration flow
import { createAuthClient } from "better-auth/react";

const client = createAuthClient();

// Register passkey
const { error } = await client.passkey.addPasskey({
  name: "My MacBook Pro",  // Friendly name for this device
});

// Sign in with passkey
const { error: signInError } = await client.signIn.passkey();
```

**Passkey UX best practices**:
- Offer passkey on first login as an upgrade, not a gate
- Allow password fallback for device switching
- Label passkeys by device type (detected from user agent)
- Passkeys sync via iCloud Keychain (Apple), Google Password Manager, Windows Hello

---

## 7. VERCEL AI SDK 5

### Agentic Patterns with maxSteps

```typescript
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";
import { anthropic } from "@ai-sdk/anthropic";
import { tool } from "ai";
import { z } from "zod";

const result = await generateText({
  model: anthropic("claude-sonnet-4-6"),
  maxSteps: 10,  // Allow up to 10 tool call rounds (agentic loop)
  tools: {
    searchWeb: tool({
      description: "Search the web for current information",
      parameters: z.object({ query: z.string() }),
      execute: async ({ query }) => {
        const results = await brave.search(query);
        return results.map(r => ({ title: r.title, url: r.url, snippet: r.description }));
      }
    }),
    readFile: tool({
      description: "Read a file from the codebase",
      parameters: z.object({ path: z.string() }),
      execute: async ({ path }) => {
        return fs.readFileSync(path, "utf-8");
      }
    }),
    writeFile: tool({
      description: "Write content to a file",
      parameters: z.object({ path: z.string(), content: z.string() }),
      execute: async ({ path, content }) => {
        fs.writeFileSync(path, content);
        return `Wrote ${content.length} bytes to ${path}`;
      }
    })
  },
  prompt: "Research the latest Next.js 16 features and create a concise summary in /tmp/summary.md"
});

console.log(result.text);  // Final text after all tool calls
console.log(result.steps);  // Full trace of all steps
```

### MCP Integration (AI SDK v5 Native)

```typescript
import { experimental_createMCPClient as createMCPClient } from "ai";
import { generateText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";

const mcpClient = await createMCPClient({
  transport: {
    type: "sse",
    url: "https://my-mcp-server.com/mcp",
  },
});

const tools = await mcpClient.tools();  // Fetch all tools from MCP server

const { text } = await generateText({
  model: anthropic("claude-opus-4-8"),
  tools,  // Pass MCP tools directly
  maxSteps: 5,
  prompt: "Create a GitHub issue for the authentication bug we discussed",
});

await mcpClient.close();
```

### Streaming UI with streamText

```typescript
// Route handler
import { streamText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";

export async function POST(request: Request) {
  const { messages } = await request.json();
  
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    messages,
    system: "You are a helpful coding assistant.",
    maxTokens: 4096,
    onFinish: async ({ text, usage }) => {
      // Save to database after completion
      await db.message.create({ data: { content: text, tokens: usage.totalTokens } });
    }
  });
  
  return result.toDataStreamResponse();
}

// Client component
"use client";
import { useChat } from "ai/react";

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat",
  });
  
  return (
    <div>
      {messages.map(m => <div key={m.id}><b>{m.role}:</b> {m.content}</div>)}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} placeholder="Ask anything..." />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

---

## 8. TESTING STACK (2025-2026)

### Vitest + Testing Library + MSW + Playwright

```typescript
// vitest.config.ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: ["./tests/setup.ts"],
    coverage: {
      provider: "v8",
      reporter: ["text", "html"],
      exclude: ["node_modules/", "tests/", "**/*.config.*"]
    }
  }
});

// tests/setup.ts
import "@testing-library/jest-dom";
import { server } from "./mocks/server";

beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

```typescript
// MSW v2 — API mocking
import { http, HttpResponse } from "msw";
import { setupServer } from "msw/node";

export const server = setupServer(
  http.get("/api/users/:id", ({ params }) => {
    return HttpResponse.json({
      id: params.id,
      name: "Test User",
      email: "test@example.com"
    });
  }),
  
  http.post("/api/posts", async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: "new-id", ...body }, { status: 201 });
  })
);

// Component test
import { render, screen, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { UserProfile } from "./UserProfile";

test("displays user data after loading", async () => {
  render(<UserProfile userId="123" />);
  
  expect(screen.getByText(/loading/i)).toBeInTheDocument();
  
  await waitFor(() => {
    expect(screen.getByText("Test User")).toBeInTheDocument();
  });
  
  expect(screen.getByText("test@example.com")).toBeInTheDocument();
});
```

```typescript
// Playwright E2E
import { test, expect } from "@playwright/test";

test.describe("Authentication flow", () => {
  test("user can sign up and sign in", async ({ page }) => {
    await page.goto("/signup");
    
    await page.fill('[name="email"]', "test@example.com");
    await page.fill('[name="password"]', "SecurePassword123!");
    await page.click('[type="submit"]');
    
    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Welcome")).toBeVisible();
    
    // Sign out and sign back in
    await page.click('[data-testid="sign-out"]');
    await page.goto("/login");
    await page.fill('[name="email"]', "test@example.com");
    await page.fill('[name="password"]', "SecurePassword123!");
    await page.click('[type="submit"]');
    
    await expect(page).toHaveURL("/dashboard");
  });
});
```

---

## 9. MONOREPO WITH TURBOREPO + PNPM

### Repository Structure

```
my-monorepo/
├── apps/
│   ├── web/              ← Next.js app
│   ├── api/              ← Hono API (Cloudflare Workers)
│   └── mobile/           ← React Native app
├── packages/
│   ├── ui/               ← Shared React components (shadcn/ui based)
│   ├── db/               ← Drizzle schema + client
│   ├── auth/             ← Better Auth config (shared)
│   └── types/            ← Shared TypeScript types
├── turbo.json
└── pnpm-workspace.yaml
```

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "globalEnv": ["NODE_ENV", "DATABASE_URL"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", ".env.local"],
      "outputs": [".next/**", "dist/**"]
    },
    "dev": {
      "persistent": true,
      "cache": false
    },
    "test": {
      "dependsOn": ["^build"],
      "outputs": ["coverage/**"]
    },
    "lint": {
      "dependsOn": ["^lint"]
    },
    "typecheck": {
      "dependsOn": ["^typecheck"]
    }
  }
}
```

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
```

```bash
# Common commands
pnpm install                     # Install all dependencies
pnpm turbo build                 # Build all affected packages
pnpm turbo dev --filter=web      # Run dev server for web app only
pnpm turbo test --filter=...ui   # Test ui package and its dependents
pnpm add -D typescript --filter=web  # Add dep to specific package
```

---

## 10. CORE WEB VITALS (2026)

### INP Replaces FID (March 2024 — Now Enforced)

Interaction to Next Paint (INP) replaced First Input Delay (FID) as a Core Web Vital. This is a ranking signal affecting SEO.

**FID**: Time until browser is ready to process first interaction (only first interaction)
**INP**: Longest interaction delay throughout entire page lifecycle (all interactions)

**INP Thresholds**:
- Good: ≤ 200ms
- Needs Improvement: 201-500ms
- Poor: > 500ms

**43% of sites still failing INP as of 2026** (Largest source of CWV regressions post-2024)

### INP Optimization Patterns

```typescript
// 1. Break up long tasks
// Bad: 200ms synchronous processing blocks main thread
function processLargeList(items: Item[]) {
  items.forEach(item => heavyComputation(item));  // Blocks for 200ms
}

// Good: yield to main thread periodically
async function processLargeList(items: Item[]) {
  for (let i = 0; i < items.length; i++) {
    heavyComputation(items[i]);
    if (i % 50 === 0) {
      await new Promise(resolve => setTimeout(resolve, 0));  // Yield
    }
  }
}

// 2. Use React transitions for non-urgent updates
import { useTransition } from "react";

function SearchInput() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  function handleSearch(e: ChangeEvent<HTMLInputElement>) {
    const value = e.target.value;
    setQuery(value);  // Urgent: update input immediately
    startTransition(() => {
      setResults(search(value));  // Non-urgent: can defer
    });
  }
  
  return (
    <>
      <input value={query} onChange={handleSearch} />
      {isPending && <Spinner />}
      <ResultsList results={results} />
    </>
  );
}

// 3. Virtualize long lists
import { useVirtualizer } from "@tanstack/react-virtual";

function VirtualList({ items }: { items: Item[] }) {
  const parentRef = useRef<HTMLDivElement>(null);
  const rowVirtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 48,
    overscan: 5
  });
  
  return (
    <div ref={parentRef} style={{ height: "400px", overflow: "auto" }}>
      <div style={{ height: rowVirtualizer.getTotalSize() }}>
        {rowVirtualizer.getVirtualItems().map(vItem => (
          <div key={vItem.key} style={{ position: "absolute", top: vItem.start, height: vItem.size }}>
            <ItemRow item={items[vItem.index]} />
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Other CWV Targets
| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP (Largest Contentful Paint) | ≤ 2.5s | 2.5-4.0s | > 4.0s |
| INP | ≤ 200ms | 200-500ms | > 500ms |
| CLS (Cumulative Layout Shift) | ≤ 0.1 | 0.1-0.25 | > 0.25 |

---

## QUICK REFERENCE: STACK DECISIONS (2026)

| Concern | Recommended | Alternative | Avoid |
|---------|------------|-------------|-------|
| Framework | Next.js 16 | Remix, Astro | Create React App (unmaintained) |
| UI Library | React 19 | Svelte 5 | Vue 3 (for new JS projects) |
| Styling | Tailwind CSS 4 | CSS Modules | Styled-components (performance) |
| Components | shadcn/ui + Radix | Chakra UI | Material UI (bundle size) |
| ORM | Drizzle v1 | Prisma 7 | TypeORM (legacy) |
| Auth | Better Auth v1 | Clerk (SaaS) | Next-Auth v4 (outdated) |
| Database | Neon (serverless) | Supabase, PlanetScale | Self-hosted Postgres (early stage) |
| API layer | tRPC v11 | Hono (REST) | Express (not typed) |
| Testing (unit) | Vitest | Jest | Mocha |
| Testing (e2e) | Playwright | Cypress | Selenium |
| Deployment | Vercel | Cloudflare Workers | AWS Lambda (cold starts) |
| Monorepo | Turborepo + pnpm | Nx | Lerna (deprecated) |
| Package manager | pnpm | npm | yarn (slower) |
| State management | Zustand / Jotai | TanStack Query | Redux (boilerplate) |
| AI/LLM | Vercel AI SDK 5 | LangChain.js | Custom fetch wrappers |
