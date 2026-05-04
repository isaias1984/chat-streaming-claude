# chat-streaming-claude

Streaming chat with [Anthropic Claude](https://www.anthropic.com) built on
the [Vercel AI SDK](https://sdk.vercel.ai) and Next.js App Router.

This repository serves two purposes:

1. **Reference implementation** — a minimal but well-structured example of
   AI streaming in Next.js (route handler + `useChat` hook, under 40 lines
   each).
2. **Workflow sandbox** — a concrete application of an AI-assisted
   development workflow using Linear, Claude Code, GitHub, and CodeRabbit.

## Status

Foundation in progress (`SETUP-001`). The chat implementation lands in
`TASK-001` once the foundation is merged.

See [`AGENTS.md`](./AGENTS.md) for the full project contract and
[`ARCHITECTURE.md`](./ARCHITECTURE.md) for the system design.

## Getting started

```bash
nvm use          # requires Node 20.11+ — see .nvmrc
npm install
cp .env.example .env.local   # add your ANTHROPIC_API_KEY
npm run dev      # http://localhost:3000
```

## Stack

- [Next.js](https://nextjs.org) (App Router)
- [Vercel AI SDK](https://sdk.vercel.ai)
- [Anthropic Claude](https://www.anthropic.com)
- TypeScript · Tailwind CSS

## Development workflow

See [`docs/plans/`](./docs/plans/) for task plans and
[`docs/conventions/`](./docs/conventions/) for coding conventions.
