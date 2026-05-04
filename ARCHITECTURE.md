# Architecture

## What this is

A Next.js App Router application that streams text responses from Anthropic
Claude through the Vercel AI SDK. Small by design — the implementation fits
in two files under 40 lines each.

## Data flow

```
Browser
  useChat() hook sends POST /api/chat with full message history
    │
    ▼
Server — src/app/api/chat/route.ts
  streamText() opens connection to Anthropic API
    │
    ▼
Anthropic API (claude-sonnet-4-5)
  Returns token stream over SSE
    │
    ▼
toDataStreamResponse() converts stream to HTTP response
    │
    ▼
Browser
  useChat() receives tokens, updates messages[] on each chunk
  React re-renders — user sees text grow in real time
```

## Key decisions

**`streamText` not `generateText`**
`generateText` waits for the complete response before returning.
`streamText` sends tokens as they arrive. For chat, always `streamText`.

**`toDataStreamResponse` not `toTextStreamResponse`**
`toDataStreamResponse` preserves metadata in the stream: token usage,
finish reason, and tool call results. `toTextStreamResponse` strips this.
Use `toDataStreamResponse` unless you have an explicit reason not to.

**API key is server-only**
The route handler runs on the server. `ANTHROPIC_API_KEY` must never use
the `NEXT_PUBLIC_` prefix — that would expose it in the browser bundle.

**`maxDuration = 60`**
Vercel serverless functions default to a 30-second timeout. Long Claude
responses can exceed this silently. Exporting `maxDuration = 60` in the
route handler raises the limit for that endpoint.

**`useChat` not manual fetch**
`useChat` manages message history, stream parsing, loading state, and
error handling. Re-implementing this adds complexity with no benefit at
this scale.

## Module boundaries

| Path              | Environment    | Rule                                                                 |
|-------------------|----------------|----------------------------------------------------------------------|
| `src/app/api/`    | Server only    | No client-side imports.                                              |
| `src/app/` pages  | Server/client  | Use `'use client'` only when the component needs hooks or browser APIs. |
| `src/lib/`        | Agnostic       | No `window`, no Node-only APIs unless filename includes `.server` or `.client`. |
| `src/components/` | Client         | Presentational only. No direct API calls, no business logic.         |
