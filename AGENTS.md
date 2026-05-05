# AGENTS.md

This is the canonical human-agent contract for `chat-streaming-claude`.
Any contributor — human or AI — reads this before touching code.

---

## Project

**chat-streaming-claude** is a streaming chat built with the Vercel AI SDK,
Next.js App Router, and Anthropic Claude. It serves two purposes:

1. A working reference implementation of AI streaming in Next.js.
2. A sandbox for validating an AI-assisted development workflow (Linear →
   Claude Code → GitHub → CodeRabbit).

**Current status**: foundation in progress. No chat implementation yet.
See `docs/plans/` for the current task queue.

---

## Active workflow modules

If a module changes, update this table and the relevant convention docs.
The rest of the workflow is unaffected.

| Concern          | Current module       | Config location                           |
|------------------|----------------------|-------------------------------------------|
| Task management  | Linear               | External (linear.app)                     |
| VCS + hosting    | GitHub               | github.com/isaias1984/chat-streaming-claude |
| AI dev agent     | Claude Code (VSCode) | `.claude/`                                |
| AI code review   | CodeRabbit           | `.coderabbit.yaml` (added in SETUP-003)   |
| CI/CD            | GitHub Actions       | `.github/workflows/` (added in SETUP-003) |

---

## Repository layout

```
.claude/
  commands/               ← Claude Code slash commands (/review, /commits)
.github/
  workflows/              ← CI/CD pipelines (wired in SETUP-003)
  pull_request_template.md
docs/
  adr/                    ← Architecture Decision Records
  conventions/            ← Granular convention docs (plain markdown)
  plans/                  ← Per-task plans (approved before code is written)
  templates/              ← Linear task and project templates
src/
  app/
    api/chat/route.ts     ← Streaming route handler (server only)
    page.tsx              ← Chat UI (client component)
  components/             ← Presentational UI (no business logic)
  lib/                    ← Shared utilities (environment-agnostic)
AGENTS.md                 ← This file
ARCHITECTURE.md           ← High-level design and key decisions
CLAUDE.md                 ← Behavioral guidelines + pointer to this file
```

---

## Canonical commands

Run from the repo root.

| Command              | Description                                 |
|----------------------|---------------------------------------------|
| `npm run dev`        | Start dev server on localhost:3000          |
| `npm run build`      | Production build                            |
| `npm run start`      | Start production server                     |
| `npm run typecheck`  | TypeScript type check |
| `npm run lint`       | ESLint check          |
| `npm run format`     | Prettier format       |
| `npm run test`       | Run unit tests        |
| `npm run test:watch` | Tests in watch mode   |

**Before every PR**: `npm run typecheck && npm run lint && npm run test`.

---

## Code conventions

- **TypeScript**: strict mode. No `any` without an inline comment
  justifying it. No `as X` assertions where a type guard works.
- **Naming**: descriptive, no unexplained abbreviations. Booleans start
  with `is`, `has`, `can`, or `should`.
- **Functions**: single responsibility. Early returns over nested
  conditionals. Max ~40 lines before considering a split.
- **Constants**: named. No magic numbers or strings inline.
- **Imports**: absolute with `@/` alias for src files. No barrel
  re-exports unless the module needs an explicit public API.
- **Error handling**: explicit. No silent catches. Errors propagate or
  are logged with context.
- **Secrets**: never commit `.env*` files. The `ANTHROPIC_API_KEY` must
  never carry the `NEXT_PUBLIC_` prefix.

See `docs/conventions/` for detailed rules per concern.

---

## Git and PR conventions

See `docs/conventions/git-github-workflow.md` for the full ruleset.

**Slash commands available in Claude Code:**

- `/review` — runs the five-block code review checklist on the current
  branch diff (BEHAVIOR SAFETY · CORRECTNESS · TESTS · LEAN CODE ·
  CONSISTENCY). Outputs PASS or NEEDS CHANGES with severity markers.
- `/commits` — reads the current branch diff and proposes atomic commits,
  a PR title (Conventional Commits), a PR body, and a ready-to-run bash
  block.

---

## Definition of Ready

A task may be planned only when it has all seven fields:
**Title · Value · Context · Acceptance Criteria · Test Plan ·
Out of Scope · References** (references only if they exist).

---

## Definition of Done

A task is done when:
- All acceptance criteria are verifiably met.
- `typecheck`, `lint`, and `test` pass locally and in CI.
- A plan document is committed inside the PR.
- The PR is reviewed (human + CodeRabbit) and merged to `main`.
- The Linear task is closed.

---

## Process thresholds

| Type                | Criteria                                        | Flow                                        |
|---------------------|-------------------------------------------------|---------------------------------------------|
| Spike / learning    | <50 lines, no public interfaces touched         | Issue + PR + self-review. No plan doc.      |
| Small feature       | Touches 1–3 files, clear bounded scope          | Full flow: plan → execute → review → PR     |
| Architecture change | Cross-cutting, affects multiple modules or APIs | Full flow + ADR in `docs/adr/`              |

---

## Known asymmetries and debt

- The original MODIFICATIONS and COMMITS prompts were in Spanish. The
  English translations are the canonical versions, installed as slash
  commands in `.claude/commands/`. The Spanish originals are not
  committed to this repo.
