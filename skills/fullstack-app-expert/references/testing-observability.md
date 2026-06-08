# Testing, CI/CD & Observability (2025–2026)

## Testing Strategy

### The Layered Approach (Not a Strict Pyramid)

The 2026 testing strategy that works in practice:

```
Unit tests (Vitest)           — Business logic, utilities, pure functions
Integration tests (Vitest)    — API routes, DB queries, server actions
Component tests (Vitest + RTL) — React components, interaction behavior
E2E tests (Playwright)        — 20–30 critical user flows
```

Getting CI feedback under 3 minutes is the target. Achieve it with:
1. Unit + integration tests first (fast, in-process)
2. Component tests next
3. E2E tests last, and only ~20–30 of the most business-critical flows

---

## Vitest — Unit & Integration Tests

Vitest is the standard for Vite/ESM projects. It's Jest-compatible but 10–20x faster (native ESM, no babel).

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',         // For component tests
    globals: true,                // No need to import describe/it/expect
    setupFiles: ['./test/setup.ts'],
    coverage: { provider: 'v8' },
  },
});
```

```typescript
// Unit test — pure function
import { describe, it, expect } from 'vitest';
import { calculateTax } from '../lib/pricing';

describe('calculateTax', () => {
  it('applies correct rate for US orders', () => {
    expect(calculateTax(100, 'US')).toBe(8.5);
  });
  
  it('returns 0 for tax-exempt items', () => {
    expect(calculateTax(100, 'US', { exempt: true })).toBe(0);
  });
});
```

```typescript
// Integration test — database query
import { describe, it, expect, beforeEach } from 'vitest';
import { db } from '../db';
import { createUser, getUser } from '../lib/users';

describe('User operations', () => {
  beforeEach(async () => {
    await db.delete(users); // Clean state before each test
  });
  
  it('creates and retrieves a user', async () => {
    const created = await createUser({ name: 'Alice', email: 'alice@test.com' });
    const fetched = await getUser(created.id);
    expect(fetched?.email).toBe('alice@test.com');
  });
});
```

### Testing React Components (Testing Library)

```tsx
import { render, screen, userEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('calls onSubmit with credentials when form is submitted', async () => {
    const user = userEvent.setup();
    const onSubmit = vi.fn();
    
    render(<LoginForm onSubmit={onSubmit} />);
    
    await user.type(screen.getByLabelText('Email'), 'user@test.com');
    await user.type(screen.getByLabelText('Password'), 'secret');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    expect(onSubmit).toHaveBeenCalledWith({
      email: 'user@test.com',
      password: 'secret',
    });
  });
});
```

---

## Playwright — E2E Tests

Playwright is the standard E2E framework in 2026 (more reliable than Cypress, faster parallel execution, better mobile emulation).

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry', // Record trace on failure for debugging
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'mobile-chrome', use: { ...devices['Pixel 7'] } },
  ],
  webServer: {
    command: 'npm run build && npm run start',
    port: 3000,
    reuseExistingServer: !process.env.CI,
  },
});
```

```typescript
// e2e/checkout.spec.ts
import { test, expect } from '@playwright/test';

test('user can complete checkout', async ({ page }) => {
  await page.goto('/products');
  await page.getByRole('button', { name: 'Add to cart' }).first().click();
  await page.goto('/cart');
  await expect(page.getByText('1 item')).toBeVisible();
  await page.getByRole('button', { name: 'Checkout' }).click();
  await page.fill('[name=email]', 'test@example.com');
  // ... complete flow
  await expect(page.getByText('Order confirmed')).toBeVisible();
});
```

**Playwright best practices:**
- Use `getByRole`, `getByLabel`, `getByText` — NOT CSS selectors (brittle)
- Keep E2E to 20–30 critical flows (login, signup, payment, core actions)
- Use Playwright's `--reporter=github` for inline PR annotations

---

## CI/CD with GitHub Actions

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push: { branches: [main] }
  pull_request: { branches: [main] }

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: test
          POSTGRES_DB: testdb
        ports: ['5432:5432']
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      
      # Run fast tests first — fail fast
      - name: Unit tests
        run: npx vitest run --reporter=github
      
      - name: Integration tests
        run: npx vitest run --project=integration
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/testdb
      
      - name: Build
        run: npm run build
      
      - name: E2E tests
        run: npx playwright test
        
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

**CI optimization tips:**
- Cache `node_modules` with `actions/setup-node`
- Run unit tests before E2E (fail fast on logic errors)
- Parallelize E2E with `shards`: `--shard=1/3`, `--shard=2/3`, `--shard=3/3`
- Use `--reporter=github` to get inline PR annotations

---

## Database Migrations in CI/CD

```typescript
// package.json scripts
{
  "db:migrate": "drizzle-kit migrate",    // Apply pending migrations
  "db:generate": "drizzle-kit generate",  // Generate new migration from schema diff
  "db:studio": "drizzle-kit studio",      // Visual DB browser
}
```

```yaml
# In CI — run migrations before integration tests
- name: Run migrations
  run: npm run db:migrate
  env:
    DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

**Migration safety:**
- Never drop columns in the same migration as removing code — deploy code first (ignore old column), then drop in a separate deployment
- Use `strict: true` in Drizzle to catch destructive changes
- Test migrations against a production snapshot in staging before deploying

---

## Observability Stack

### The Three Pillars

**Errors (Sentry):** Install Sentry and configure it to capture both server and client errors. Sentry now integrates with OpenTelemetry traces, connecting errors to their trace context.

```typescript
// Next.js Sentry setup (sentry.server.config.ts)
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 0.1,  // Sample 10% of transactions
  environment: process.env.NODE_ENV,
  integrations: [
    Sentry.prismaIntegration(),  // Trace DB queries
  ],
});
```

**Tracing + Metrics (OpenTelemetry):** OTel is the vendor-neutral standard. Instrument once, send to any backend (Grafana, Datadog, Honeycomb).

```typescript
// instrumentation.ts (Next.js 15+)
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    const { NodeSDK } = await import('@opentelemetry/sdk-node');
    const { OTLPTraceExporter } = await import('@opentelemetry/exporter-trace-otlp-http');
    
    const sdk = new NodeSDK({
      traceExporter: new OTLPTraceExporter({
        url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT,
      }),
    });
    sdk.start();
  }
}
```

**Logging:** Use structured JSON logs (not `console.log`). Pino is the fastest Node.js logger.

```typescript
import pino from 'pino';

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  formatters: {
    level: (label) => ({ level: label }),
  },
});

// Structured log — queryable in Grafana Loki / Datadog
logger.info({ userId, action: 'checkout_started', amount }, 'Checkout initiated');
```

### Feature Flags

Use feature flags to decouple deployment from release:
- **PostHog** (open-source, also analytics + session replay)
- **LaunchDarkly** (enterprise)
- **Statsig** (experimentation-focused)

```typescript
// PostHog feature flags
import { useFeatureFlagEnabled } from 'posthog-js/react';

function NewCheckout() {
  const isEnabled = useFeatureFlagEnabled('new-checkout-v2');
  if (!isEnabled) return <OldCheckout />;
  return <CheckoutV2 />;
}
```
