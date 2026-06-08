# Security for Full Stack Apps (2025–2026)

## OWASP Top 10 (2025 Edition)

| Rank | Risk | Key Prevention |
|---|---|---|
| A01 | Broken Access Control | Check permissions server-side on every request; never trust client |
| A02 | Security Misconfiguration | Secure headers, disable debug modes, minimal surface area |
| A03 | Injection (SQL, command, LDAP) | Parameterized queries, ORMs, never string-concatenate SQL |
| A04 | Insecure Design | Threat model at design time, not post-hoc |
| A05 | Vulnerable Components | `npm audit`, Dependabot, pin versions |
| A06 | Identification & Auth Failures | MFA, passkeys, don't roll your own auth |
| A07 | Software Integrity Failures | Verify supply chain, signed packages |
| A08 | Security Logging Failures | Log security events, alert on anomalies |
| A09 | Cryptographic Failures | TLS everywhere, bcrypt/argon2 for passwords |
| A10 | Mishandled Exceptions | Never expose stack traces in production |

---

## Input Validation with Zod

**Rule:** Validate ALL input at the boundary (API routes, Server Actions, tRPC procedures). Never trust anything from the client.

```typescript
import { z } from 'zod';

// Define schema once, use everywhere
const CreateUserSchema = z.object({
  name: z.string().min(1).max(100).trim(),
  email: z.string().email().toLowerCase(),
  age: z.number().int().min(18).max(120),
  role: z.enum(['user', 'admin']).default('user'),
  bio: z.string().max(500).optional(),
});

// Next.js Server Action
async function createUser(formData: FormData) {
  'use server';
  
  const result = CreateUserSchema.safeParse({
    name: formData.get('name'),
    email: formData.get('email'),
    age: Number(formData.get('age')),
  });
  
  if (!result.success) {
    return { error: result.error.flatten() };
  }
  
  // result.data is fully typed and validated
  await db.insert(users).values(result.data);
}
```

---

## SQL Injection Prevention

**Never concatenate user input into SQL strings.** Always use parameterized queries or an ORM.

```typescript
// BAD — SQL injection vulnerability
const users = await db.query(`SELECT * FROM users WHERE email = '${email}'`);

// GOOD — Drizzle ORM (parameterized automatically)
const users = await db.select().from(usersTable).where(eq(usersTable.email, email));

// GOOD — Raw SQL with parameters (Drizzle sql tag)
const users = await db.execute(sql`SELECT * FROM users WHERE email = ${email}`);
// Never: sql`... WHERE email = '${email}'` — this bypasses parameterization
```

---

## XSS Prevention

React escapes JSX output by default. The main XSS attack surface in React apps:

```tsx
// DANGEROUS — bypasses React's escaping
<div dangerouslySetInnerHTML={{ __html: userContent }} />

// SAFE — use a sanitizer if you must render HTML
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userContent) }} />

// BEST — avoid dangerouslySetInnerHTML entirely; use a markdown renderer
import { marked } from 'marked';
import DOMPurify from 'dompurify';
const safeHtml = DOMPurify.sanitize(marked.parse(markdownContent));
```

**Content Security Policy (CSP) header:**
```typescript
// next.config.js — strict CSP
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: [
      "default-src 'self'",
      "script-src 'self' 'nonce-{nonce}'",  // Use nonces, not unsafe-inline
      "style-src 'self' 'unsafe-inline'",    // Tailwind requires this
      "img-src 'self' data: https:",
      "connect-src 'self' https://api.yourapp.com",
      "frame-ancestors 'none'",
    ].join('; '),
  },
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
  { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
];
```

---

## CSRF Protection

**Next.js Server Actions are protected by default** — they compare `Origin` header against `Host`, blocking cross-origin POST requests.

For custom API Routes with cookie-based auth:
```typescript
// Verify Origin header for state-mutating routes
export async function POST(req: Request) {
  const origin = req.headers.get('origin');
  const host = req.headers.get('host');
  
  if (origin && new URL(origin).host !== host) {
    return new Response('Forbidden', { status: 403 });
  }
  // ... proceed
}

// Or use CSRF token library
import { csrf } from '@edge-csrf/nextjs';
```

**Cookie settings for session cookies:**
```typescript
// Secure cookie configuration
const sessionCookieOptions = {
  httpOnly: true,      // JS cannot access — prevents XSS cookie theft
  secure: true,        // HTTPS only
  sameSite: 'lax',     // CSRF protection (allows top-level navigations)
  path: '/',
  maxAge: 60 * 60 * 24 * 7,  // 7 days
};
```

---

## Rate Limiting

**Always rate limit:**
- Auth endpoints (login, signup, password reset)
- AI/LLM endpoints
- Any expensive operation
- Public API endpoints

```typescript
// middleware.ts — rate limit at the edge with Upstash
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';
import { NextRequest } from 'next/server';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(20, '10 s'),
  analytics: true,  // Track in Upstash console
});

export async function middleware(request: NextRequest) {
  // Rate limit auth routes
  if (request.nextUrl.pathname.startsWith('/api/auth')) {
    const ip = request.ip ?? request.headers.get('x-forwarded-for') ?? 'unknown';
    const { success, remaining, reset } = await ratelimit.limit(`auth:${ip}`);
    
    if (!success) {
      return new Response('Too Many Requests', {
        status: 429,
        headers: {
          'Retry-After': String(Math.ceil((reset - Date.now()) / 1000)),
          'X-RateLimit-Remaining': String(remaining),
        },
      });
    }
  }
}
```

---

## CORS Configuration

```typescript
// Hono CORS middleware
import { cors } from 'hono/cors';

app.use('/api/*', cors({
  origin: (origin) => {
    const allowedOrigins = [
      'https://app.yourcompany.com',
      process.env.NODE_ENV === 'development' ? 'http://localhost:3000' : null,
    ].filter(Boolean);
    return allowedOrigins.includes(origin) ? origin : null;
  },
  allowMethods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowHeaders: ['Content-Type', 'Authorization'],
  maxAge: 86400,  // Cache preflight for 24 hours
}));
```

**CORS rule:** Never use `origin: '*'` for APIs that handle authenticated requests or sensitive data. Wildcard CORS is fine only for truly public, read-only APIs.

---

## Secrets Management

```typescript
// .env.local — never commit to git
DATABASE_URL=postgresql://...
STRIPE_SECRET_KEY=sk_...
OPENAI_API_KEY=sk-...

// Validate env at startup with Zod (t3-env pattern)
import { createEnv } from '@t3-oss/env-nextjs';
import { z } from 'zod';

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    STRIPE_SECRET_KEY: z.string().startsWith('sk_'),
    OPENAI_API_KEY: z.string().startsWith('sk-'),
  },
  client: {
    NEXT_PUBLIC_APP_URL: z.string().url(),
  },
  runtimeEnv: process.env,
});
// App crashes at startup with clear error if secrets are missing
```

**In production:** Use platform secret management:
- Vercel: Environment Variables in dashboard
- AWS: Secrets Manager or Parameter Store
- Cloudflare: Secrets via `wrangler secret put`

**Never log secrets.** Set up log scrubbing to redact API keys, tokens, and passwords from structured logs.

---

## Security Checklist for Launch

- [ ] All inputs validated with Zod at API boundaries
- [ ] Parameterized queries only (no string-concatenated SQL)
- [ ] Auth checked server-side on every protected route/action
- [ ] Security headers configured (CSP, X-Frame-Options, etc.)
- [ ] Rate limiting on auth and expensive endpoints
- [ ] Cookies: httpOnly, Secure, SameSite=Lax
- [ ] `npm audit` clean (no high/critical vulnerabilities)
- [ ] Secrets in environment variables, not hardcoded
- [ ] Error messages don't expose stack traces or internal details in production
- [ ] Dependencies pinned and Dependabot/Renovate enabled
- [ ] CORS configured explicitly (no wildcard for authenticated APIs)
