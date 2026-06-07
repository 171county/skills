---
name: full-stack-app-expert
description: Expert-level reference for full-stack engineers and tech leads building production web applications in 2025–2026. Use when asked about React 19, Next.js 15, TypeScript advanced patterns, Drizzle/Prisma, authentication, REST/GraphQL/tRPC, deployment, AI SDK integration, testing with Vitest/Playwright, Core Web Vitals, OWASP security, or Turborepo monorepos.
---

# Full Stack App Expert

## 1. The 2026 Full-Stack Standard Stack

### Canonical TypeScript Stack (2026)

```
Frontend:
  React 19 + Next.js 15 (App Router) OR Remix v3
  TypeScript 5.x (strict mode required)
  Tailwind CSS v4 + shadcn/ui (or Radix UI primitives)
  Zustand (client state) + TanStack Query v5 (server state)

Backend:
  Next.js API routes OR Hono v4 (edge-native, ultra-fast)
  Drizzle ORM (type-safe, lightweight) OR Prisma 7 (DX)
  PostgreSQL (primary) + Redis (caching/sessions)

Auth:
  Better Auth (full-featured, framework-agnostic)
  OR Auth.js v5 (Next.js native) 

AI Integration:
  Vercel AI SDK 4.x (streaming, structured output, agents)

Build & Deploy:
  Turborepo (monorepo) + pnpm workspaces
  Vercel (frontend) OR Fly.io (full-stack) OR Railway

Testing:
  Vitest (unit/integration) + Playwright (E2E)
  Testing Library (component tests)

Infrastructure:
  Docker (local dev) + GitHub Actions (CI/CD)
  Cloudflare (CDN, Workers, KV, R2)
```

---

## 2. React 19 and Next.js 15

### React 19 Key Features

**React Compiler (automatic memo):**
React 19 includes the React Compiler (formerly React Forget), which automatically memoizes components, eliminating the need for `useMemo`, `useCallback`, and `memo` in most cases.

```tsx
// Before React 19: manual optimization required
const ExpensiveComponent = React.memo(({ data }) => {
  const processed = useMemo(() => processData(data), [data]);
  const handler = useCallback(() => handleClick(processed), [processed]);
  return <div onClick={handler}>{processed.name}</div>;
});

// React 19 with compiler: no manual memoization needed
function ExpensiveComponent({ data }) {
  const processed = processData(data);  // Compiler handles memoization
  const handler = () => handleClick(processed);
  return <div onClick={handler}>{processed.name}</div>;
}
```

**Actions (form handling):**
```tsx
// React 19: native async actions
async function submitForm(formData: FormData) {
  "use server";  // Server Action (Next.js)
  const name = formData.get("name");
  await db.insert(users).values({ name });
  revalidatePath("/users");
}

function UserForm() {
  const [state, action, isPending] = useActionState(submitForm, null);
  
  return (
    <form action={action}>
      <input name="name" />
      <button type="submit" disabled={isPending}>
        {isPending ? "Submitting..." : "Submit"}
      </button>
      {state?.error && <p>{state.error}</p>}
    </form>
  );
}
```

**useOptimistic for optimistic updates:**
```tsx
function TodoList({ todos }: { todos: Todo[] }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo: string) => [...state, { id: "temp", text: newTodo, done: false }]
  );
  
  async function handleAdd(formData: FormData) {
    const text = formData.get("text") as string;
    addOptimisticTodo(text);  // Immediate UI update
    await createTodo(text);   // Server action (may revert on failure)
  }
  
  return (
    <div>
      {optimisticTodos.map(todo => <TodoItem key={todo.id} todo={todo} />)}
      <form action={handleAdd}>
        <input name="text" /><button type="submit">Add</button>
      </form>
    </div>
  );
}
```

### Next.js 15 App Router Patterns

**Route structure:**
```
app/
├── layout.tsx           (root layout, persistent across navigation)
├── page.tsx             (/)
├── loading.tsx          (Suspense boundary fallback)
├── error.tsx            (Error boundary)
├── not-found.tsx        (404)
├── (marketing)/         (route group — no URL segment)
│   ├── about/page.tsx
│   └── pricing/page.tsx
├── dashboard/
│   ├── layout.tsx       (nested layout)
│   └── page.tsx         (/dashboard)
└── api/
    └── webhooks/
        └── route.ts     (API route handler)
```

**Server Components (default in App Router):**
```tsx
// Server Component — runs on server, no JS sent to client
// Can be async, access DB directly, no hooks
async function ProductPage({ params }: { params: { id: string } }) {
  // Direct DB access in server component
  const product = await db.query.products.findFirst({
    where: eq(products.id, params.id)
  });
  
  if (!product) notFound();
  
  return (
    <div>
      <h1>{product.name}</h1>
      <ProductPrice price={product.price} />
      <AddToCartButton productId={product.id} />  {/* Client Component */}
    </div>
  );
}

// Client Component — explicitly marked, has interactivity
"use client";
function AddToCartButton({ productId }: { productId: string }) {
  const [added, setAdded] = useState(false);
  return (
    <button onClick={() => setAdded(true)}>
      {added ? "Added!" : "Add to Cart"}
    </button>
  );
}
```

**Parallel and Intercepted Routes:**
```
app/
├── @modal/             (parallel route — modal slot)
│   └── (.)photo/[id]/
│       └── page.tsx    (intercepted route — modal on same page)
└── photo/[id]/
    └── page.tsx        (full page when navigating directly)
```

**Next.js 15 caching (explicit opt-in/out):**
```tsx
// Next.js 15: No automatic fetch caching (changed from v13/14)
// Must explicitly opt in to caching

// Cached: static data
const data = await fetch("https://api.example.com/config", {
  next: { revalidate: 3600 }  // Revalidate every hour
});

// Uncached (default in v15): fresh every request
const user = await fetch(`https://api.example.com/users/${id}`);

// unstable_cache for non-fetch data (DB queries)
import { unstable_cache } from "next/cache";

const getCachedUser = unstable_cache(
  async (id: string) => db.query.users.findFirst({ where: eq(users.id, id) }),
  ["user"],
  { revalidate: 60, tags: [`user-${userId}`] }
);
```

---

## 3. TypeScript Advanced Patterns

### Discriminated Unions for Type Safety

```typescript
// Model API responses with discriminated unions
type ApiResult<T> = 
  | { status: "success"; data: T; requestId: string }
  | { status: "error"; error: string; code: number }
  | { status: "loading" };

function handleResult<T>(result: ApiResult<T>) {
  switch (result.status) {
    case "success":
      return result.data;  // TypeScript knows T here
    case "error":
      throw new Error(`${result.code}: ${result.error}`);
    case "loading":
      return null;
  }
}
```

### Template Literal Types

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type ApiRoute = `/api/${string}`;

type RouteConfig = {
  [K in `${Lowercase<HttpMethod>}:${ApiRoute}`]: RouteHandler;
};
// Gives type-safe route definitions like "get:/api/users", "post:/api/products"
```

### Branded Types for Domain Safety

```typescript
// Prevent mixing up different ID types
declare const UserIdBrand: unique symbol;
declare const OrderIdBrand: unique symbol;

type UserId = string & { [UserIdBrand]: never };
type OrderId = string & { [OrderIdBrand]: never };

function createUserId(id: string): UserId {
  return id as UserId;
}

function getUser(id: UserId) { /* ... */ }
function getOrder(id: OrderId) { /* ... */ }

const userId = createUserId("user_123");
getUser(userId);      // ✓
getOrder(userId);     // TypeScript error: UserId not assignable to OrderId
```

### Zod for Runtime Validation

```typescript
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1).max(100),
  email: z.string().email(),
  role: z.enum(["admin", "user", "viewer"]),
  createdAt: z.coerce.date(),  // string → Date conversion
  metadata: z.record(z.unknown()).optional(),
});

type User = z.infer<typeof UserSchema>;  // TypeScript type from schema

// Validate API input
const parseUserInput = (raw: unknown): User => {
  const result = UserSchema.safeParse(raw);
  if (!result.success) {
    throw new ValidationError(result.error.flatten());
  }
  return result.data;
};
```

---

## 4. Database Layer: Drizzle and Prisma 7

### Drizzle ORM (Recommended for New Projects)

Drizzle is SQL-first, type-safe, and production-proven. Lightweight, no query overhead.

**Schema definition:**
```typescript
import { pgTable, uuid, varchar, timestamp, text, boolean, pgEnum } from "drizzle-orm/pg-core";
import { relations } from "drizzle-orm";

export const userRoleEnum = pgEnum("user_role", ["admin", "user", "viewer"]);

export const users = pgTable("users", {
  id: uuid("id").primaryKey().defaultRandom(),
  name: varchar("name", { length: 100 }).notNull(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  role: userRoleEnum("role").notNull().default("user"),
  emailVerified: boolean("email_verified").notNull().default(false),
  createdAt: timestamp("created_at").notNull().defaultNow(),
  updatedAt: timestamp("updated_at").notNull().defaultNow().$onUpdate(() => new Date()),
});

export const posts = pgTable("posts", {
  id: uuid("id").primaryKey().defaultRandom(),
  title: varchar("title", { length: 200 }).notNull(),
  content: text("content"),
  authorId: uuid("author_id").notNull().references(() => users.id, { onDelete: "cascade" }),
  publishedAt: timestamp("published_at"),
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));
export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] }),
}));
```

**Database queries:**
```typescript
import { db } from "@/lib/db";
import { users, posts } from "@/lib/schema";
import { eq, and, desc, count, like } from "drizzle-orm";

// Select with relation
const userWithPosts = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: {
    posts: {
      where: eq(posts.publishedAt, null),  // drafts only
      orderBy: [desc(posts.createdAt)],
      limit: 10,
    }
  }
});

// Complex join
const postsWithAuthors = await db
  .select({ post: posts, authorName: users.name })
  .from(posts)
  .innerJoin(users, eq(posts.authorId, users.id))
  .where(and(
    like(posts.title, "%TypeScript%"),
    eq(users.role, "admin")
  ))
  .orderBy(desc(posts.createdAt))
  .limit(20);

// Insert with returning
const [newUser] = await db
  .insert(users)
  .values({ name, email })
  .returning();

// Update with condition
await db
  .update(users)
  .set({ emailVerified: true, updatedAt: new Date() })
  .where(eq(users.email, email));

// Aggregate query
const [{ total }] = await db
  .select({ total: count() })
  .from(posts)
  .where(eq(posts.authorId, userId));
```

**Migrations:**
```bash
# Generate migration from schema changes
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate

# Push schema directly to DB (dev only)
npx drizzle-kit push
```

### Prisma 7 (Alternative — Strong DX)

Prisma 7 (November 2025) eliminated the bundle size gap that made Prisma problematic for edge/serverless:

```prisma
// schema.prisma
generator client {
  provider = "prisma-client-js"
  output   = "../src/generated/prisma"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id           String   @id @default(uuid())
  name         String   @db.VarChar(100)
  email        String   @unique @db.VarChar(255)
  role         UserRole @default(user)
  posts        Post[]
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}

model Post {
  id          String   @id @default(uuid())
  title       String   @db.VarChar(200)
  content     String?
  author      User     @relation(fields: [authorId], references: [id])
  authorId    String
  publishedAt DateTime?
}

enum UserRole {
  admin
  user
  viewer
}
```

```typescript
// Prisma 7 query
const userWithPosts = await prisma.user.findUnique({
  where: { id: userId },
  include: {
    posts: {
      where: { publishedAt: null },
      orderBy: { createdAt: "desc" },
      take: 10
    }
  }
});
```

**Drizzle vs. Prisma 7 decision:**
| Factor | Drizzle | Prisma 7 |
|---|---|---|
| Bundle size | Tiny | Resolved in v7 |
| SQL control | Full (SQL-first) | Abstracted |
| Type safety | Excellent | Excellent |
| Migration UX | Good (drizzle-kit) | Excellent |
| Edge runtime | Yes | Yes (v7) |
| Learning curve | Medium | Low |
| Choose when | SQL power user, performance-critical | Rapid prototyping, DX priority |

---

## 5. Authentication: Better Auth

Better Auth is the recommended auth library in 2026 — framework-agnostic, type-safe, feature-complete.

```typescript
// auth.ts — server setup
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { db } from "@/lib/db";
import { twoFactor, organization, admin } from "better-auth/plugins";

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: "pg",
    schema: { users, sessions, accounts, verifications },
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
    twoFactor({ issuer: "MyApp" }),
    organization(),  // Multi-tenant org support
    admin(),         // Admin panel and user management
  ],
  
  session: {
    expiresIn: 60 * 60 * 24 * 7,  // 7 days
    updateAge: 60 * 60 * 24,      // Refresh every day
    cookieCache: { enabled: true, maxAge: 5 * 60 },  // Client-side cache
  },
  
  rateLimit: {
    window: 10,       // 10-second window
    max: 100,         // 100 requests max
    storage: "redis", // Redis for distributed rate limiting
  },
});
```

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

```typescript
// Server component — get session
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

export async function getSession() {
  return auth.api.getSession({ headers: await headers() });
}

// Protect a route
async function ProtectedPage() {
  const session = await getSession();
  if (!session) redirect("/login");
  return <div>Hello, {session.user.name}</div>;
}
```

---

## 6. API Design: REST, tRPC, and GraphQL

### tRPC (Type-Safe RPC — Recommended for Full-Stack TypeScript)

```typescript
// server/routers/users.ts
import { z } from "zod";
import { router, publicProcedure, protectedProcedure } from "../trpc";

export const usersRouter = router({
  getAll: publicProcedure
    .input(z.object({ limit: z.number().min(1).max(100).default(20), cursor: z.string().optional() }))
    .query(async ({ input }) => {
      const { limit, cursor } = input;
      const users = await db.query.users.findMany({
        limit: limit + 1,
        cursor: cursor ? { id: cursor } : undefined,
      });
      return {
        items: users.slice(0, limit),
        nextCursor: users.length > limit ? users[limit].id : undefined,
      };
    }),
  
  create: protectedProcedure
    .input(z.object({ name: z.string().min(1), email: z.string().email() }))
    .mutation(async ({ input, ctx }) => {
      const [user] = await db.insert(users).values(input).returning();
      return user;
    }),
  
  delete: protectedProcedure
    .input(z.string().uuid())
    .mutation(async ({ input: userId, ctx }) => {
      if (ctx.session.user.role !== "admin") {
        throw new TRPCError({ code: "FORBIDDEN" });
      }
      await db.delete(users).where(eq(users.id, userId));
    }),
});

// Client usage — fully type-safe, no code gen needed
const { data, isLoading } = trpc.users.getAll.useQuery({ limit: 20 });
const createUser = trpc.users.create.useMutation({
  onSuccess: () => trpc.users.getAll.invalidate()
});
```

### Hono for Edge-Native API

```typescript
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { z } from "zod";

const app = new Hono();

const createUserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
});

app.post(
  "/users",
  zValidator("json", createUserSchema),
  async (c) => {
    const { name, email } = c.req.valid("json");
    const [user] = await db.insert(users).values({ name, email }).returning();
    return c.json({ user }, 201);
  }
);

app.get("/users/:id", async (c) => {
  const { id } = c.req.param();
  const user = await db.query.users.findFirst({ where: eq(users.id, id) });
  if (!user) return c.json({ error: "Not found" }, 404);
  return c.json({ user });
});

export default app;
```

---

## 7. AI SDK Integration (Vercel AI SDK 4.x)

```typescript
import { anthropic } from "@ai-sdk/anthropic";
import { generateText, streamText, generateObject } from "ai";
import { z } from "zod";

// Streaming chat endpoint
async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    system: "You are a helpful assistant.",
    messages,
    tools: {
      searchDatabase: {
        description: "Search the product database",
        parameters: z.object({ query: z.string() }),
        execute: async ({ query }) => searchProducts(query),
      },
    },
    maxSteps: 5,  // Allow up to 5 agentic steps
  });
  
  return result.toDataStreamResponse();
}

// Structured output generation
async function analyzeDocument(text: string) {
  const { object } = await generateObject({
    model: anthropic("claude-opus-4-8"),
    schema: z.object({
      summary: z.string(),
      keyPoints: z.array(z.string()),
      sentiment: z.enum(["positive", "neutral", "negative"]),
      topics: z.array(z.string()),
      confidenceScore: z.number().min(0).max(1),
    }),
    prompt: `Analyze this document:\n\n${text}`,
  });
  
  return object;  // Fully typed!
}

// Client-side chat with streaming
"use client";
import { useChat } from "@ai-sdk/react";

function ChatComponent() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat",
    onError: (error) => console.error(error),
  });
  
  return (
    <div>
      {messages.map(m => (
        <div key={m.id} className={m.role === "user" ? "text-right" : "text-left"}>
          {m.content}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

---

## 8. State Management

### Zustand (Client State)

```typescript
import { create } from "zustand";
import { persist } from "zustand/middleware";
import { immer } from "zustand/middleware/immer";

interface CartStore {
  items: CartItem[];
  addItem: (product: Product, quantity: number) => void;
  removeItem: (productId: string) => void;
  updateQuantity: (productId: string, quantity: number) => void;
  clearCart: () => void;
  total: () => number;
}

const useCartStore = create<CartStore>()(
  persist(
    immer((set, get) => ({
      items: [],
      
      addItem: (product, quantity) => set(state => {
        const existing = state.items.find(i => i.productId === product.id);
        if (existing) {
          existing.quantity += quantity;
        } else {
          state.items.push({ productId: product.id, product, quantity });
        }
      }),
      
      removeItem: (productId) => set(state => {
        state.items = state.items.filter(i => i.productId !== productId);
      }),
      
      updateQuantity: (productId, quantity) => set(state => {
        const item = state.items.find(i => i.productId === productId);
        if (item) item.quantity = quantity;
      }),
      
      clearCart: () => set({ items: [] }),
      
      total: () => get().items.reduce(
        (sum, item) => sum + item.product.price * item.quantity, 
        0
      ),
    })),
    { name: "cart-storage" }  // Persisted to localStorage
  )
);
```

### TanStack Query v5 (Server State)

```typescript
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

// Query with automatic refetching
function UserProfile({ userId }: { userId: string }) {
  const { data: user, isLoading, error } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetch(`/api/users/${userId}`).then(r => r.json()),
    staleTime: 5 * 60 * 1000,  // 5 minutes before refetch
    gcTime: 30 * 60 * 1000,    // 30 minutes in cache
  });
  
  if (isLoading) return <Skeleton />;
  if (error) return <Error message={error.message} />;
  return <div>{user.name}</div>;
}

// Mutation with optimistic update
function useUpdateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (data: { id: string; name: string }) =>
      fetch(`/api/users/${data.id}`, {
        method: "PATCH",
        body: JSON.stringify(data),
      }).then(r => r.json()),
    
    onMutate: async (newUser) => {
      await queryClient.cancelQueries({ queryKey: ["user", newUser.id] });
      const previousUser = queryClient.getQueryData(["user", newUser.id]);
      queryClient.setQueryData(["user", newUser.id], old => ({ ...old, ...newUser }));
      return { previousUser };  // Return for rollback
    },
    
    onError: (err, newUser, context) => {
      queryClient.setQueryData(["user", newUser.id], context?.previousUser);
    },
    
    onSettled: (data, error, newUser) => {
      queryClient.invalidateQueries({ queryKey: ["user", newUser.id] });
    },
  });
}
```

---

## 9. Testing

### Vitest (Unit and Integration)

```typescript
import { describe, it, expect, vi, beforeEach } from "vitest";
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import { userEvent } from "@testing-library/user-event";

// Unit test
describe("calculateDiscount", () => {
  it("applies 10% discount for premium users", () => {
    expect(calculateDiscount(100, "premium")).toBe(90);
  });
  
  it("applies no discount for regular users", () => {
    expect(calculateDiscount(100, "regular")).toBe(100);
  });
});

// Component test with mocks
describe("UserProfile", () => {
  beforeEach(() => {
    vi.mock("@/lib/api", () => ({
      getUser: vi.fn().mockResolvedValue({ id: "1", name: "Alice", email: "alice@test.com" })
    }));
  });
  
  it("renders user name after loading", async () => {
    const user = userEvent.setup();
    render(<UserProfile userId="1" />);
    
    expect(screen.getByText("Loading...")).toBeInTheDocument();
    await waitFor(() => {
      expect(screen.getByText("Alice")).toBeInTheDocument();
    });
  });
  
  it("handles edit submission", async () => {
    const user = userEvent.setup();
    const onSave = vi.fn();
    render(<UserProfile userId="1" onSave={onSave} />);
    
    await user.click(screen.getByRole("button", { name: /edit/i }));
    await user.clear(screen.getByLabelText(/name/i));
    await user.type(screen.getByLabelText(/name/i), "Bob");
    await user.click(screen.getByRole("button", { name: /save/i }));
    
    expect(onSave).toHaveBeenCalledWith(expect.objectContaining({ name: "Bob" }));
  });
});
```

### Playwright (E2E)

```typescript
import { test, expect } from "@playwright/test";

test.describe("User Authentication Flow", () => {
  test("user can sign up and log in", async ({ page }) => {
    // Navigate to signup
    await page.goto("/signup");
    
    // Fill form
    await page.getByLabel("Email").fill("test@example.com");
    await page.getByLabel("Password").fill("SecurePassword123!");
    await page.getByRole("button", { name: "Create Account" }).click();
    
    // Verify email verification message
    await expect(page.getByText("Check your email")).toBeVisible();
    
    // Simulate email verification (test helper)
    await verifyEmailInTest(page, "test@example.com");
    
    // Should redirect to dashboard
    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Welcome back")).toBeVisible();
  });
  
  test("shows error for invalid credentials", async ({ page }) => {
    await page.goto("/login");
    await page.getByLabel("Email").fill("nonexistent@example.com");
    await page.getByLabel("Password").fill("wrongpassword");
    await page.getByRole("button", { name: "Log In" }).click();
    
    await expect(page.getByRole("alert")).toContainText("Invalid email or password");
  });
});

// Visual regression testing
test("landing page matches snapshot", async ({ page }) => {
  await page.goto("/");
  await expect(page).toHaveScreenshot("landing-page.png", {
    fullPage: true,
    maxDiffPixels: 100,
  });
});
```

---

## 10. Performance: Core Web Vitals

### 2026 Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | <2.5s | 2.5–4.0s | >4.0s |
| **INP** (Interaction to Next Paint) | <200ms | 200–500ms | >500ms |
| **CLS** (Cumulative Layout Shift) | <0.1 | 0.1–0.25 | >0.25 |
| **FCP** (First Contentful Paint) | <1.8s | 1.8–3.0s | >3.0s |
| **TTFB** (Time to First Byte) | <800ms | 800ms–1.8s | >1.8s |

**Key optimizations:**

**LCP (image loading):**
```tsx
// Above-the-fold hero image
import Image from "next/image";

function HeroSection() {
  return (
    <Image
      src="/hero.webp"
      alt="Hero image"
      width={1920}
      height={1080}
      priority           // Preloads — use only for above-fold
      sizes="100vw"      // Responsive sizes
      quality={85}
      placeholder="blur" // Show blur while loading
    />
  );
}
```

**INP (interaction responsiveness):**
```tsx
// Avoid blocking the main thread
// Use startTransition for non-urgent updates
import { startTransition, useState } from "react";

function SearchInput() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  
  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    setQuery(e.target.value);  // Urgent: update input immediately
    startTransition(() => {
      setResults(searchData(e.target.value));  // Non-urgent: filter results
    });
  }
  
  return <input value={query} onChange={handleChange} />;
}
```

**CLS (layout stability):**
```tsx
// Reserve space for dynamic content
function AdBanner() {
  return (
    <div style={{ minHeight: 250, width: "100%" }}>  {/* Reserve space */}
      <Suspense fallback={<div style={{ height: 250 }} />}>  {/* Fallback with same height */}
        <AsyncAdContent />
      </Suspense>
    </div>
  );
}
```

---

## 11. Security (OWASP Top 10 for Web Apps)

### Critical Implementations

**SQL Injection Prevention (use ORM — Drizzle/Prisma always parameterize):**
```typescript
// NEVER do this:
const query = `SELECT * FROM users WHERE id = '${userId}'`;  // SQL INJECTION RISK

// Always use Drizzle/Prisma (parameterized automatically):
const user = await db.query.users.findFirst({
  where: eq(users.id, userId)  // Safe — parameterized
});
```

**XSS Prevention:**
```typescript
// React automatically escapes JSX expressions
// DANGER: dangerouslySetInnerHTML with user content
const SafeContent = ({ html }: { html: string }) => (
  <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />  // Sanitize first!
);

// Content Security Policy (Next.js)
// next.config.ts
const securityHeaders = [
  { key: "Content-Security-Policy", value: cspHeader },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "X-Frame-Options", value: "DENY" },
  { key: "Strict-Transport-Security", value: "max-age=63072000; includeSubDomains; preload" },
];
```

**CSRF Protection:**
```typescript
// Better Auth handles CSRF tokens automatically
// For custom endpoints: use SameSite=Strict cookies + verify Origin header

async function POST(req: Request) {
  const origin = req.headers.get("origin");
  if (origin !== process.env.NEXT_PUBLIC_APP_URL) {
    return new Response("Forbidden", { status: 403 });
  }
  // ...
}
```

**Rate Limiting:**
```typescript
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, "10 s"),  // 10 requests per 10 seconds
});

export async function middleware(request: NextRequest) {
  const ip = request.ip ?? "127.0.0.1";
  const { success, limit, reset, remaining } = await ratelimit.limit(ip);
  
  if (!success) {
    return new NextResponse("Too Many Requests", { 
      status: 429,
      headers: {
        "X-RateLimit-Limit": limit.toString(),
        "X-RateLimit-Remaining": remaining.toString(),
        "X-RateLimit-Reset": reset.toString(),
      }
    });
  }
}
```

---

## 12. Monorepo with Turborepo

```
my-app/
├── apps/
│   ├── web/          (Next.js frontend)
│   ├── api/          (Hono/Express API)
│   └── docs/         (Documentation site)
├── packages/
│   ├── ui/           (Shared component library)
│   ├── db/           (Drizzle schema + client)
│   ├── auth/         (Shared auth utilities)
│   └── tsconfig/     (Shared TypeScript config)
├── turbo.json
└── package.json
```

**turbo.json:**
```json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"],
      "outputs": [".next/**", "dist/**"]
    },
    "test": {
      "dependsOn": ["^build"],
      "inputs": ["src/**/*.ts", "src/**/*.tsx", "test/**"]
    },
    "lint": {
      "inputs": ["src/**/*.ts", "src/**/*.tsx"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

**pnpm-workspace.yaml:**
```yaml
packages:
  - "apps/*"
  - "packages/*"
```

**Turborepo benefits:**
- **Remote caching**: CI builds cache task outputs in cloud; subsequent runs skip unchanged packages
- **Parallel execution**: runs independent tasks in parallel automatically
- **Incremental builds**: only rebuilds what changed (git-aware)
- **TypeScript project references**: integrated type-checking across packages
