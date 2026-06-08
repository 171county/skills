# Database Architecture (2025–2026)

## The Default: PostgreSQL

PostgreSQL is the right default for 95% of full stack apps. Use something else only when you have a specific reason.

### Managed PostgreSQL Options

| Provider | Best For | Notes |
|---|---|---|
| Supabase | Full-stack apps, built-in Auth/Storage/Realtime | Built-in pgBouncer, pgvector, Row Level Security |
| Neon | Serverless/edge, branching, cost at idle | Scales to zero, HTTP API for edge, DB branching for PRs |
| PlanetScale/Vitess | Huge scale, MySQL compat | Vitess sharding; MySQL not Postgres |
| Railway | Simple projects, good DX | Persistent server model |
| AWS RDS/Aurora | Enterprise, existing AWS infra | Most operational overhead |
| CockroachDB | Multi-region, global apps | PostgreSQL wire compatible, automatic sharding |

### Connection Pooling: Always Use PgBouncer

Raw PostgreSQL handles ~100 concurrent connections. With serverless functions spawning connection per-request, you'll exhaust connections instantly.

**Solution:** PgBouncer in transaction pooling mode.
- Supabase: built-in, use port 6543 instead of 5432
- Neon: HTTP API + connection pooling built-in
- Self-hosted: deploy PgBouncer as a sidecar

```
# Supabase — use pooler URL for serverless
DATABASE_URL="postgresql://user:pass@db.supabase.co:6543/postgres?pgbouncer=true"

# Direct connection for migrations (PgBouncer doesn't support all migration commands)
DIRECT_DATABASE_URL="postgresql://user:pass@db.supabase.co:5432/postgres"
```

### PostgreSQL Index Strategy

```sql
-- B-tree (default): equality and range queries
CREATE INDEX idx_users_email ON users(email);

-- Partial index: only index rows matching a condition (smaller, faster)
CREATE INDEX idx_active_users ON users(created_at) WHERE status = 'active';

-- Composite index: column order matters — most selective or most filtered first
CREATE INDEX idx_orders ON orders(user_id, status, created_at DESC);

-- GIN index for JSONB and full-text search
CREATE INDEX idx_metadata ON products USING GIN (metadata);

-- BRIN for time-series / append-only tables (tiny index, fast for ranges)
CREATE INDEX idx_events_time ON events USING BRIN (created_at);
```

**Query optimization checklist:**
1. `EXPLAIN ANALYZE` every slow query (look for Seq Scans on large tables)
2. Add index for every foreign key (PostgreSQL doesn't auto-create them)
3. Use `SELECT` only the columns you need
4. Avoid `N+1` queries — use joins or `findMany` with `include` in ORMs

---

## ORM Selection: Drizzle vs Prisma

### The 2026 Verdict

**Drizzle for serverless/edge:** 3–5x faster cold starts (75ms vs 115ms after Prisma 7 improvement, vs 1500ms pre-Prisma 7). Schema-as-TypeScript means instant type updates.

**Prisma for warm servers:** Better migration tooling, supports more databases (MongoDB, SQL Server), and the developer experience for large schemas is still the best in the ecosystem.

### Drizzle ORM

```typescript
// Schema definition (TypeScript-first, no DSL)
import { pgTable, text, timestamp, uuid } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').notNull().unique(),
  name: text('name'),
  createdAt: timestamp('created_at').defaultNow(),
});

// Fully typed queries that look like SQL
import { db } from './db';
import { eq, and, gte } from 'drizzle-orm';

const activeUsers = await db
  .select({ id: users.id, email: users.email })
  .from(users)
  .where(and(
    eq(users.status, 'active'),
    gte(users.createdAt, new Date('2025-01-01'))
  ))
  .limit(50);

// Drizzle Kit for migrations
// drizzle.config.ts
export default {
  schema: './src/db/schema.ts',
  out: './drizzle',
  driver: 'pg',
  dbCredentials: { connectionString: process.env.DATABASE_URL! },
};
// ALWAYS use strict: true to prevent accidental column drops
```

**Drizzle gotcha:** Without `strict: true`, a renamed column is interpreted as "drop + add" — data loss. Always run `drizzle-kit generate` with `--strict` in production workflows.

### Prisma 7

```typescript
// schema.prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

// Prisma Client usage
const users = await prisma.user.findMany({
  where: { status: 'active' },
  select: { id: true, email: true },
  orderBy: { createdAt: 'desc' },
  take: 50,
});
```

Prisma 7 (Nov 2025) replaced the Rust query engine with TypeScript/WASM, cutting bundle size from ~14MB to ~1.6MB and cold starts from ~1500ms to ~115ms. Edge runtime support is now native.

---

## Redis

Redis is required the moment you need any of:
- Rate limiting (Upstash Redis for serverless)
- Session storage
- Pub/sub or real-time events
- Distributed job queues (BullMQ)
- Leaderboards / sorted sets
- API response caching

**Upstash** is the serverless Redis — HTTP-based, edge-compatible, pay-per-request. Perfect for Vercel/Cloudflare deployments.

```typescript
// Upstash rate limiting — runs at the edge
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'), // 10 req per 10 seconds
});

// In Next.js middleware or API route
const { success, remaining } = await ratelimit.limit(ip);
if (!success) return new Response('Rate limited', { status: 429 });
```

**Redis Streams** (vs BullMQ): Use Streams for high-throughput event processing where consumer groups matter. Use BullMQ for job queues with retries, delays, and priorities.

---

## Vector Databases & AI Search

### pgvector — Start Here

pgvector is a PostgreSQL extension for vector similarity search. It's the right default for apps that already use PostgreSQL.

```sql
-- Enable extension
CREATE EXTENSION vector;

-- Store embeddings alongside regular data
CREATE TABLE documents (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  content text,
  embedding vector(1536),  -- OpenAI ada-002 dimension
  created_at timestamptz DEFAULT now()
);

-- HNSW index (fast ANN, <10ms at 99th percentile for <5M vectors)
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);

-- Similarity search
SELECT id, content, 1 - (embedding <=> $1) AS similarity
FROM documents
ORDER BY embedding <=> $1  -- <=> is cosine distance
LIMIT 10;
```

**pgvector 0.8+ (late 2025):** Included by default on AWS RDS, Google Cloud SQL, Supabase, and Neon. HNSW indexes keep query latency under 10ms at p99 for up to ~5M vectors on a standard Supabase Pro instance.

### When to Use Dedicated Vector DBs (Pinecone, Weaviate, Qdrant)

Switch from pgvector to a dedicated vector DB when:
- Dataset exceeds 10–20M vectors (pgvector HNSW memory usage scales linearly)
- You need advanced filtering at scale (metadata filtering is more optimized in Pinecone/Qdrant)
- You need multi-tenancy with namespace isolation

**Qdrant** is the open-source pick for self-hosted (Rust-based, excellent performance). **Pinecone** for managed, serverless.

---

## NoSQL & Specialized Databases

### MongoDB
Use when: Document-oriented data with variable schema, rapid prototyping, content management. Avoid for relational data with many joins — that's what PostgreSQL is for.

### DynamoDB
Use when: On AWS, need single-digit millisecond at any scale, key-value or simple access patterns. High learning curve for complex queries (no joins). Use DynamoDB only if access patterns are well-defined upfront.

### TimescaleDB
PostgreSQL extension for time-series data. Use for: metrics, IoT, financial data, event logs. Adds automatic partitioning by time, compression (95%+ size reduction), and time-series-specific functions.

```sql
-- Create hypertable (automatically partitions by time)
SELECT create_hypertable('sensor_readings', 'time');

-- Compression (compress chunks older than 7 days)
ALTER TABLE sensor_readings SET (
  timescaledb.compress,
  timescaledb.compress_segmentby = 'device_id'
);
SELECT add_compression_policy('sensor_readings', INTERVAL '7 days');
```

### Database Selection Cheat Sheet

```
Relational data, most apps              → PostgreSQL
Time-series / metrics / IoT             → TimescaleDB (PostgreSQL extension)
Global multi-region, always available   → CockroachDB (PostgreSQL wire compat)
Document storage, flexible schema       → MongoDB or PostgreSQL JSONB
High-throughput key-value on AWS        → DynamoDB
Caching, queues, pub-sub                → Redis (Upstash for serverless)
Vector/semantic search                  → pgvector (start here), Qdrant/Pinecone at scale
Search full-text                        → PostgreSQL FTS, Typesense, Meilisearch
```
