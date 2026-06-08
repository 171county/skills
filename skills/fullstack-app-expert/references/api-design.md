# API Design Patterns (2025–2026)

## The 2026 Multi-Protocol Reality

No single API protocol wins everywhere. The mature 2026 architecture uses:
- **tRPC** for internal TypeScript frontend ↔ backend communication
- **REST + OpenAPI 3.1** for public APIs and cross-team/cross-language integrations
- **gRPC** for service-to-service communication in microservices
- **GraphQL** for complex graph data or multi-client apps with different field requirements

---

## tRPC v11 — TypeScript Internal APIs

tRPC eliminates the client/server type contract entirely — end-to-end type safety without code generation, schemas, or separate documentation.

### Setup (Next.js App Router + tRPC v11)

```typescript
// server/trpc.ts
import { initTRPC } from '@trpc/server';
import { Context } from './context';

const t = initTRPC.context<Context>().create();
export const router = t.router;
export const publicProcedure = t.procedure;
export const protectedProcedure = t.procedure.use(({ ctx, next }) => {
  if (!ctx.session?.user) throw new TRPCError({ code: 'UNAUTHORIZED' });
  return next({ ctx: { ...ctx, user: ctx.session.user } });
});

// server/routers/users.ts
import { z } from 'zod';

export const usersRouter = router({
  list: protectedProcedure
    .input(z.object({ limit: z.number().default(20) }))
    .query(async ({ input, ctx }) => {
      return db.query.users.findMany({ limit: input.limit });
    }),
  
  create: protectedProcedure
    .input(z.object({ name: z.string(), email: z.string().email() }))
    .mutation(async ({ input, ctx }) => {
      return db.insert(users).values(input).returning();
    }),
});

// Client usage — fully typed, no import of server code
const { data } = trpc.users.list.useQuery({ limit: 10 });
const createUser = trpc.users.create.useMutation();
// TypeScript knows the exact shape of data and input types
```

**tRPC v11 + Server Components (2025 feature):**
```typescript
// Call tRPC procedures directly from Server Components
// No client boundary needed
const caller = createCaller(await createContext());
const users = await caller.users.list({ limit: 10 });
```

**When NOT to use tRPC:**
- Public APIs (external consumers can't use tRPC unless they're TypeScript)
- When you need OpenAPI documentation for third-party integrations
- Non-TypeScript backends

---

## REST + OpenAPI 3.1

REST is the right choice for public APIs. OpenAPI 3.1 (aligned with JSON Schema) is the current standard.

### Code-First OpenAPI with Hono

```typescript
import { OpenAPIHono, createRoute } from '@hono/zod-openapi';
import { z } from 'zod';

const app = new OpenAPIHono();

const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string(),
  email: z.string().email(),
}).openapi('User');

const createUserRoute = createRoute({
  method: 'post',
  path: '/users',
  request: {
    body: { content: { 'application/json': { schema: UserSchema.omit({ id: true }) } } }
  },
  responses: {
    201: { content: { 'application/json': { schema: UserSchema } }, description: 'Created' },
    400: { description: 'Validation error' },
  },
});

app.openapi(createUserRoute, async (c) => {
  const body = c.req.valid('json');
  const user = await db.insert(users).values(body).returning();
  return c.json(user[0], 201);
});

// Auto-generates OpenAPI 3.1 spec at /doc
app.doc('/doc', { openapi: '3.1.0', info: { title: 'API', version: '1.0.0' } });
```

### REST Versioning Strategies

**URL versioning** (most common, most pragmatic):
```
/api/v1/users
/api/v2/users  (breaking changes get a new major version)
```

**Header versioning** (cleaner URLs, harder to test):
```
Accept: application/vnd.myapi.v2+json
```

**Deprecation pattern:** Never delete v1 without a 6-month sunset window. Add `Deprecation` and `Sunset` response headers.

---

## GraphQL

Use GraphQL when:
- Multiple clients (web, mobile, third-party) need different subsets of the same data
- Data is genuinely graph-shaped with many relationship traversals
- You want a single endpoint with self-documenting schema

### Apollo Server 4 (Node.js)

```typescript
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

const typeDefs = `#graphql
  type User {
    id: ID!
    name: String!
    posts: [Post!]!
  }
  type Query {
    users(limit: Int = 20): [User!]!
    user(id: ID!): User
  }
  type Mutation {
    createUser(name: String!, email: String!): User!
  }
`;

const resolvers = {
  Query: {
    users: async (_, { limit }) => db.query.users.findMany({ limit }),
    user: async (_, { id }) => db.query.users.findFirst({ where: eq(users.id, id) }),
  },
  User: {
    posts: async (user) => db.query.posts.findMany({ where: eq(posts.userId, user.id) }),
  },
};
```

**GraphQL N+1 problem — always use DataLoader:**
```typescript
import DataLoader from 'dataloader';

const userPostsLoader = new DataLoader(async (userIds: string[]) => {
  const posts = await db.select().from(postsTable)
    .where(inArray(postsTable.userId, userIds));
  // Group posts by userId and return in same order as input
  return userIds.map(id => posts.filter(p => p.userId === id));
});
```

**When NOT to use GraphQL:**
- Simple CRUD APIs (over-engineering)
- Performance-critical paths (JSON serialization overhead, no HTTP caching)
- Small teams without GraphQL expertise

---

## gRPC (Service-to-Service)

gRPC is 3–10x faster than REST+JSON for internal service communication, uses HTTP/2, and enforces a typed contract via Protocol Buffers.

```protobuf
// user.proto
syntax = "proto3";

service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc ListUsers (ListUsersRequest) returns (stream User); // Server streaming
}

message GetUserRequest { string id = 1; }
message User {
  string id = 1;
  string name = 2;
  string email = 3;
}
```

**Use gRPC when:**
- Backend microservices communicating with each other (not client ↔ server)
- High-throughput data transfer (streaming, binary encoding)
- Strong contract enforcement across teams/languages

**Don't use gRPC for:**
- Browser clients (requires grpc-web proxy layer)
- Simple CRUD APIs where REST is perfectly fine

---

## Backend for Frontend (BFF) Pattern

When different clients (mobile app, web app, admin dashboard) have very different data requirements, a BFF aggregates multiple backend services per client type:

```
Mobile App → Mobile BFF → [User Service, Order Service, Product Service]
Web App    → Web BFF    → [User Service, Order Service, Product Service]
Admin      → Admin BFF  → [All services + admin-specific endpoints]
```

Each BFF:
- Returns only the fields that client needs (reduces payload)
- Handles client-specific auth flows
- Aggregates and transforms data for that client
- Can be a thin tRPC or REST layer

**In practice with Next.js:** The Next.js App Router + Server Components effectively IS your Web BFF. Server Components fetch from multiple services and compose exactly the data each page needs, with no client-side orchestration.
