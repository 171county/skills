# AI Integration in Full Stack Apps (2025–2026)

## Vercel AI SDK — The Standard

The Vercel AI SDK (v4+, major v6 released Dec 2025) is the de facto standard for integrating LLMs into web apps. Key value props:
- **Provider-agnostic:** Switch between OpenAI, Anthropic Claude, Google Gemini, Mistral, and 25+ others by changing 2 lines
- **Streaming-first:** ReadableStream-based, works on edge runtimes
- **Full-stack:** Server functions + React hooks that work together
- **Tool calling + Zod:** Native structured output and function calling

---

## Streaming Chat with Next.js

```typescript
// app/api/chat/route.ts — Server route
import { openai } from '@ai-sdk/openai';  // or @ai-sdk/anthropic, etc.
import { streamText } from 'ai';

export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = await streamText({
    model: openai('gpt-4o'),
    // model: anthropic('claude-sonnet-4-5'),  // Drop-in replacement
    system: 'You are a helpful assistant.',
    messages,
    maxTokens: 1000,
  });
  
  return result.toDataStreamResponse();
}
```

```tsx
// app/chat/page.tsx — Client Component
'use client';
import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
  });
  
  return (
    <div>
      <div className="messages">
        {messages.map(m => (
          <div key={m.id} className={m.role === 'user' ? 'user-msg' : 'ai-msg'}>
            {m.content}
          </div>
        ))}
        {isLoading && <div className="animate-pulse">Thinking...</div>}
      </div>
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

---

## Structured Output with Zod

Force the LLM to return typed, validated JSON:

```typescript
import { generateObject } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const ProductSchema = z.object({
  name: z.string(),
  price: z.number().positive(),
  category: z.enum(['electronics', 'clothing', 'food']),
  features: z.array(z.string()).max(5),
});

const { object } = await generateObject({
  model: openai('gpt-4o'),
  schema: ProductSchema,
  prompt: 'Extract product information from: ' + rawText,
});
// object is fully typed as z.infer<typeof ProductSchema>
```

---

## Tool Calling (Function Calling)

Tools allow the LLM to call functions in your app:

```typescript
import { streamText, tool } from 'ai';
import { z } from 'zod';

const result = await streamText({
  model: openai('gpt-4o'),
  messages,
  tools: {
    searchProducts: tool({
      description: 'Search the product catalog',
      parameters: z.object({
        query: z.string(),
        maxPrice: z.number().optional(),
        category: z.string().optional(),
      }),
      execute: async ({ query, maxPrice, category }) => {
        // This runs on YOUR server — not the LLM
        const products = await db.select().from(productsTable)
          .where(ilike(productsTable.name, `%${query}%`))
          .limit(5);
        return products;
      },
    }),
    
    getWeather: tool({
      description: 'Get current weather for a city',
      parameters: z.object({ city: z.string() }),
      execute: async ({ city }) => {
        return fetch(`https://api.weather.example.com/${city}`).then(r => r.json());
      },
    }),
  },
  maxSteps: 5,  // Allow multi-step tool calls
});
```

---

## AI RSC — Server-Side AI Rendering

For apps where AI content should be in the initial HTML (no loading spinner on first paint):

```tsx
// app/actions.tsx — Server Action with AI streaming
'use server';
import { streamUI } from 'ai/rsc';
import { openai } from '@ai-sdk/openai';

export async function generateProductDescription(productId: string) {
  const product = await db.query.products.findFirst({
    where: eq(products.id, productId)
  });
  
  const result = await streamUI({
    model: openai('gpt-4o'),
    prompt: `Write a product description for: ${product.name}`,
    text: ({ content, done }) => (
      // Streams React components from server to client
      <div className={done ? 'complete' : 'streaming'}>
        {content}
      </div>
    ),
  });
  
  return result.value;
}
```

---

## AI-Powered Semantic Search

```typescript
// 1. Generate embeddings for new content
import { embed, embedMany } from 'ai';
import { openai } from '@ai-sdk/openai';

async function indexDocument(text: string, docId: string) {
  const { embedding } = await embed({
    model: openai.embedding('text-embedding-3-small'),
    value: text,
  });
  
  await db.insert(documents).values({
    id: docId,
    content: text,
    embedding: embedding,  // pgvector column
  });
}

// 2. Semantic search query
async function semanticSearch(query: string, limit = 5) {
  const { embedding } = await embed({
    model: openai.embedding('text-embedding-3-small'),
    value: query,
  });
  
  // pgvector cosine similarity search
  return db.execute(sql`
    SELECT id, content, 1 - (embedding <=> ${embedding}::vector) AS similarity
    FROM documents
    ORDER BY embedding <=> ${embedding}::vector
    LIMIT ${limit}
  `);
}

// 3. RAG (Retrieval Augmented Generation)
async function ragQuery(userQuestion: string) {
  const relevantDocs = await semanticSearch(userQuestion);
  
  const { text } = await generateText({
    model: openai('gpt-4o'),
    system: `Answer based on these documents:\n${relevantDocs.map(d => d.content).join('\n\n')}`,
    prompt: userQuestion,
  });
  
  return text;
}
```

---

## Production AI Patterns

**Cost optimization with model routing:**
```typescript
// Route simple queries to cheap models, complex ones to capable models
function selectModel(query: string) {
  const isSimple = query.length < 100 && !query.includes('analyze');
  return isSimple
    ? openai('gpt-4o-mini')     // ~$0.15/M input tokens
    : openai('gpt-4o');          // ~$2.50/M input tokens
}
// Teams using this pattern report 40–60% cost reduction
```

**Streaming UX patterns:**
- Show a typing indicator immediately (before first token)
- Stream tokens as they arrive (not waiting for completion)
- Allow users to stop generation
- Auto-scroll to follow the stream

**Rate limiting AI endpoints:**
```typescript
// AI endpoints are expensive — rate limit aggressively
const aiRatelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(5, '1 m'), // 5 AI requests per minute per user
});
```

**Error handling for LLM failures:**
```typescript
import { RetryError, APIError } from 'ai';

try {
  const result = await generateText({ model, prompt });
} catch (error) {
  if (error instanceof APIError && error.status === 429) {
    // Rate limited by provider — implement exponential backoff
    await new Promise(r => setTimeout(r, 1000 * attempt));
  }
  // Always have a fallback response
  return { text: "I'm having trouble right now. Please try again." };
}
```
