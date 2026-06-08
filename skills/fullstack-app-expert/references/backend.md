# Backend Frameworks & Runtimes (2025–2026)

## Runtime Selection: Node.js vs Bun vs Deno

### Bun in Production (2026)

Bun has crossed the production threshold. Key facts:
- ~4x faster startup than Node.js, 2–3x faster HTTP throughput in benchmarks
- Built-in TypeScript transpilation (no `ts-node`, no `tsx`)
- Built-in test runner (`bun test`), bundler, package manager
- Node.js compatibility layer covers ~95% of npm packages
- Used in production at scale (Vercel, Cloudflare Workers runtime, multiple unicorns)

```bash
# Bun-native HTTP server — no framework overhead
Bun.serve({
  port: 3000,
  fetch(req) {
    return new Response("Hello World");
  },
});
```

**When to use Bun:** New greenfield APIs, CLI tools, scripts, Cloudflare Workers deployment, any TypeScript-first backend without legacy Node.js dependencies.

**When to stay on Node.js:** Legacy codebases, native C++ addons, or when you need 100% npm ecosystem compatibility (e.g., some database drivers).

---

## HTTP Framework Selection

### Hono — The 2026 Default for New APIs

Hono is the fastest general-purpose TypeScript HTTP framework, designed for Web Standards (uses `Request`/`Response` natively), and runs on every runtime: Node.js, Bun, Deno, Cloudflare Workers, AWS Lambda.

```typescript
import { Hono } from 'hono';
import { zValidator } from '@hono/zod-validator';
import { z } from 'zod';

const app = new Hono();

const schema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
});

app.post('/users', zValidator('json', schema), async (c) => {
  const body = c.req.valid('json'); // Fully typed, validated
  const user = await db.insert(users).values(body).returning();
  return c.json(user[0], 201);
});

// Middleware
app.use('*', async (c, next) => {
  const start = Date.now();
  await next();
  console.log(`${c.req.method} ${c.req.url} - ${Date.now() - start}ms`);
});

export default app;
```

**Hono + Bun benchmark:** ~120K req/s on commodity hardware for simple JSON responses (vs ~45K for Express on Node.js).

**Hono + Cloudflare Workers:**
```typescript
// Deploy to 300+ edge PoPs with zero config
export default app; // Hono app is a valid CF Worker export
```

### Fastify — Production Node.js Powerhouse

Fastify is the right choice when:
- You're staying on Node.js (team familiarity, existing codebase)
- You want a rich plugin ecosystem (`@fastify/auth`, `@fastify/cors`, `@fastify/rate-limit`)
- Schema-first validation with JSON Schema / TypeBox is desired

```typescript
import Fastify from 'fastify';
import { Type } from '@sinclair/typebox';

const fastify = Fastify({ logger: true });

const UserSchema = Type.Object({
  name: Type.String(),
  email: Type.String({ format: 'email' }),
});

fastify.post<{ Body: typeof UserSchema }>('/users', {
  schema: { body: UserSchema },
}, async (request, reply) => {
  const user = await db.insert(users).values(request.body).returning();
  return reply.code(201).send(user[0]);
});
```

**Performance:** Fastify delivers ~80K req/s on Node.js — 2x Express, competitive with Hono on Node.js.

### Express — Legacy Only

Express is in maintenance mode as of 2026. Performance is 2–3x slower than Fastify/Hono. Use only when:
- Maintaining existing Express codebase
- Using a library that ships Express middleware with no Hono/Fastify equivalent
- Never for new projects

---

## Python Backends

### FastAPI — The Standard

FastAPI is the default Python web framework for new projects in 2026:
- Async-first (built on Starlette + Uvicorn)
- Automatic OpenAPI 3.1 generation from type hints
- Pydantic v2 validation (10–50x faster than v1 due to Rust core)

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, EmailStr
from typing import Optional

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: EmailStr

@app.post("/users", status_code=201)
async def create_user(user: UserCreate) -> dict:
    # Pydantic validates email format automatically
    result = await db.execute(
        "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *",
        user.name, user.email
    )
    return result.fetchone()
```

**FastAPI + async database:** Use `asyncpg` or `databases` library for non-blocking PostgreSQL queries. Avoid SQLAlchemy sync operations in async FastAPI routes.

### Django in 2026

Django still dominates for:
- Admin-heavy internal tools (Django Admin is unmatched)
- Complex data models with relationships (ORM is excellent)
- Batteries-included: auth, sessions, forms, migrations
- Teams that want convention over configuration

**Django + async:** Django 4.1+ supports async views and ORM queries. Use `sync_to_async` for legacy synchronous code.

---

## Go Backends

Go is the right backend choice when:
- Raw throughput and memory efficiency matter (handles 2–5x more concurrent connections than Node.js at same RAM)
- Building long-running services, not serverless functions
- Team has Go expertise

### Gin vs Fiber vs Echo

| Framework | Best For |
|---|---|
| Gin | Mature, most popular, excellent middleware ecosystem |
| Fiber | Express-like API, fastest of the three (uses fasthttp) |
| Echo | Minimalist, good for REST APIs, strong middleware |

```go
// Gin — production-standard Go HTTP
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    
    r.POST("/users", func(c *gin.Context) {
        var user UserCreate
        if err := c.ShouldBindJSON(&user); err != nil {
            c.JSON(400, gin.H{"error": err.Error()})
            return
        }
        result := db.Create(&user)
        c.JSON(201, user)
    })
    
    r.Run(":8080")
}
```

**Go for gRPC services:** Go is the dominant language for gRPC microservices. `google.golang.org/grpc` is mature and performant.

---

## Backend Architecture Decision Framework

```
New TypeScript API (serverless/edge):
  → Hono on Bun (or Cloudflare Workers)

New TypeScript API (persistent server):
  → Fastify on Node.js or Bun

New Python API:
  → FastAPI (async) for new projects
  → Django for admin-heavy or ORM-heavy apps

Need raw performance / microservices / gRPC:
  → Go (Gin or Fiber)

Background jobs / queues:
  → BullMQ (Node.js/Bun) or Temporal for durable workflows
  → Inngest for serverless background jobs with retries

WebSockets / real-time:
  → Socket.IO (Node.js), Partykit (edge), or Supabase Realtime
```
