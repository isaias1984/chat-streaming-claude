# SETUP-001 — Execution context for Claude Code

## What you are and what to do

You are Claude Code running inside VSCode on the repository
`chat-streaming-claude` (github.com/isaias1984/chat-streaming-claude).

The repo already has the Next.js scaffold on `main` (committed in Phase B).
Your task is to create the `setup/001-foundation` branch and add all the
governance and contract files listed below, exactly as specified.

**Do not modify any file the scaffold already created unless the
instructions below explicitly say so.** The only scaffold files we touch
are `README.md`, `.gitignore`, and `package.json`.

When all files are created, stop and wait. Do not commit anything yet —
the commit step is at the end and uses the `/commits` slash command.

---

## Step 1 — Create the branch

```bash
git checkout -b setup/001-foundation
```

---

## Step 2 — Create these files

### `LICENSE`

```
MIT License

Copyright (c) 2026 Isaías Redondo Vilchez

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

### `.nvmrc`

```
20.11.0
```

---

### `CLAUDE.md`

```markdown
# CLAUDE.md

## Behavioral guidelines

Behavioral guidelines to reduce common LLM coding mistakes, derived from
Andrej Karpathy's observations on LLM coding pitfalls.

**Tradeoff:** These guidelines bias toward caution over speed.
For trivial tasks, use judgment.

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?"
If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

After every change, run:
```bash
npm run typecheck && npm run lint && npm run test
```

Strong success criteria let you loop independently.
Weak criteria ("make it work") require constant clarification.

---

These guidelines are working if: fewer unnecessary changes in diffs,
fewer rewrites due to overcomplication, and clarifying questions come
before implementation rather than after mistakes.

---

## Project contract

For conventions, repository structure, canonical commands, workflow
modules, Definition of Ready, and Definition of Done for this project,
see [AGENTS.md](./AGENTS.md).
```

---

### `AGENTS.md`

```markdown
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

Run from the repo root. Commands marked ⚠️ are declared but not wired
yet — they print an informational message and exit 0 until SETUP-002.

| Command              | Description                                 |
|----------------------|---------------------------------------------|
| `npm run dev`        | Start dev server on localhost:3000          |
| `npm run build`      | Production build                            |
| `npm run start`      | Start production server                     |
| `npm run typecheck`  | TypeScript type check ⚠️ wired in SETUP-002 |
| `npm run lint`       | ESLint check ⚠️ wired in SETUP-002          |
| `npm run format`     | Prettier format ⚠️ wired in SETUP-002       |
| `npm run test`       | Run unit tests ⚠️ wired in SETUP-002        |
| `npm run test:watch` | Tests in watch mode ⚠️ wired in SETUP-002   |

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
- `typecheck`, `lint`, `format`, `test`, and `test:watch` are declared
  but not wired. They will be implemented in SETUP-002.
```

---

### `ARCHITECTURE.md`

```markdown
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
```

---

### `README.md` (replace the scaffold version entirely)

```markdown
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
```

---

### `.env.example`

```
# Copy this file to .env.local and fill in your key.
# Never commit .env.local — it is in .gitignore.
ANTHROPIC_API_KEY=sk-ant-...
```

---

### `.gitignore` — append to the existing scaffold file

Add these lines at the end of the existing `.gitignore`. Do not remove
anything the scaffold already put there.

```
# Additional ignores beyond the Next.js scaffold default

# Environment files — never commit secrets
.env
.env.local
.env.*.local
.env.production

# Editor caches
.idea/
*.iml

# OS noise
.DS_Store
Thumbs.db

# Agent and tool caches
.claude/cache/
```

---

### `package.json` — modify the existing file

Add the `engines` field and the missing scripts. Do not remove any
existing fields. The `engines` field goes at the top level (alongside
`name`, `version`, etc.). The result for `scripts` should be:

```json
"engines": {
  "node": ">=20.11.0"
},
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "next lint",
  "typecheck": "echo 'typecheck: not configured yet — see SETUP-002' && exit 0",
  "format": "echo 'format: not configured yet — see SETUP-002' && exit 0",
  "test": "echo 'test: not configured yet — see SETUP-002' && exit 0",
  "test:watch": "echo 'test:watch: not configured yet — see SETUP-002' && exit 0"
}
```

---

### `docs/conventions/README.md`

```markdown
# Conventions

This directory holds focused convention documents — one file per concern.
All are referenced from [`AGENTS.md`](../../AGENTS.md).

| File                      | Covers                                          |
|---------------------------|-------------------------------------------------|
| `git-github-workflow.md`  | Commit format, branch naming, PR conventions    |

New conventions are added here as the project grows. Each file should be
short, opinionated, and directly actionable.
```

---

### `docs/conventions/git-github-workflow.md`

```markdown
# Git and GitHub workflow

## Commits on feature branches

- **Format**: imperative mood, American English, no period at the end.
  Example: `Add streaming route handler`
- **No conventional commit prefixes** on branch commits unless explicitly
  requested. Prefixes are reserved for the squash commit on `main`.
- **No issue or task IDs** in branch commit messages.
- **Atomic**: each commit does one thing. Changes that are a direct
  consequence of each other go in the same commit.
- **Optional body**: follow the 50/72 rule. Explain *what* and *why*,
  not *how*.

## PR title and squash commit on `main`

- **Format**: Conventional Commits without scope.
  `feat:`, `fix:`, or `chore:` — nothing else without discussion.
  Example: `feat: add streaming chat route handler`
- **No issue or task IDs** in the PR title.

## `git add` rules

- Always use explicit file paths: `git add src/app/page.tsx`.
- Never use `.`, `-A`, `-u`, or directory names.
- The sum of files in all `git add` calls must equal
  `git status --porcelain`. If you intentionally exclude a file,
  justify it in a comment.

## What not to do without being explicitly asked

- `git push` to any branch other than your working branch
- `git rebase`, `git merge`
- `gh pr create`

## Branch naming

```
<type>/<short-description>
```

Types: `feat`, `fix`, `chore`, `setup`, `docs`, `test`.
Examples: `feat/streaming-route`, `setup/001-foundation`, `fix/stream-cutoff`.
```

---

### `docs/plans/README.md`

```markdown
# Plans

Every non-trivial PR includes a plan document in this directory.

## Rules

1. The plan is written and approved **before** any code is generated.
2. The plan is committed **as part of the PR** it describes.
3. Plans are never deleted — they are the audit trail of why things
   were built the way they were.

## Format

Each plan file is named after the task it describes: `SETUP-001.md`,
`TASK-001.md`, etc. Plans follow this structure:

- **Context** — why this work exists
- **Goal** — one sentence
- **Out of scope** — explicit, links to follow-up tasks if applicable
- **Decisions** — design choices with alternatives and rationale
- **Files to create / modify**
- **Acceptance criteria** — observable, verifiable
- **Test plan** — how to verify it is done
- **Risks** — honest assessment with mitigations
```

---

### `docs/plans/SETUP-001.md`

Copy the content of the `SETUP-001.md` file you downloaded earlier in
this session verbatim into this file.

---

### `docs/adr/README.md`

```markdown
# Architecture Decision Records

This directory holds Architecture Decision Records (ADRs) following the
[Nygard format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions).

## Rules

- One ADR per significant cross-cutting decision.
- ADRs are numbered sequentially starting at `0001`.
- Once accepted, an ADR is **never edited**. If a decision changes,
  write a new ADR that supersedes the old one and link them.
- Not every decision needs an ADR — only those that are non-obvious,
  affect multiple parts of the system, or are likely to be questioned
  later.
```

---

### `docs/adr/0001-record-architecture-decisions.md`

```markdown
# ADR 0001 — Record architecture decisions

## Status

Accepted

## Context

The project needs a way to capture significant architecture decisions so
that future contributors (human or AI) understand why the system is built
the way it is, not just how.

Without a record, decisions made early in a project become invisible
constraints that nobody remembers or can challenge constructively.

## Decision

We will use Architecture Decision Records (ADRs) stored in `docs/adr/`,
following the format introduced by Michael Nygard.

Each ADR records: the context that made a decision necessary, the decision
itself, and its consequences. ADRs are immutable once accepted.

## Consequences

- Future contributors can read the history of technical decisions.
- Disagreeing with a decision has a defined process: write a new ADR
  that supersedes the old one.
- The directory may grow large over time. That is a feature, not a bug.
```

---

### `docs/templates/linear-task.md`

```markdown
# Task template

Copy this template when creating a task in Linear.

---

**Title**
[Imperative verb + what changes. Example: "Add streaming route handler"]

**Value**
[What this task unblocks or enables. One or two sentences.]

**Context**
[Situation this task addresses. Relevant constraints or decisions already
made. Link to parent plan or ADR if applicable.]

**Acceptance criteria**
- [ ] [Observable, verifiable condition 1]
- [ ] [Observable, verifiable condition 2]

**Test plan**
[Which tests are added or modified. How to verify the criteria manually
if automated tests don't cover something.]

**Out of scope**
[What is explicitly NOT done in this task. Link to the follow-up task
if one exists.]

**References**
[Only if they exist: docs, PRs, ADRs, external links.]
```

---

### `docs/templates/linear-project.md`

```markdown
# Project template

Copy this template when creating a project in Linear.

---

**Title**
[Short, descriptive name for the project.]

**Value**
[Who benefits and why this project matters. One paragraph max.]

**Context**
[Current situation, relevant constraints, decisions already taken before
this project started.]

**Out of scope**
[What this project explicitly does NOT deliver. Be specific — this field
prevents scope creep.]

**Test plan (high level)**
[How we will know the project is done. The overall signal that the
project objective has been met, not individual task criteria.]

**References**
[Only if they exist: related projects, external docs, ADRs.]
```

---

### `.claude/commands/review.md`

```markdown
Run a structured code review on the current branch diff.

First, get the diff:
```bash
git diff main...HEAD
```

Evaluate it across the five blocks below. Be specific: file name and,
where possible, line number or symbol name.

---

### 1) BEHAVIOR SAFETY ← review this first

- Can any change alter behaviour already covered by existing tests?
- Have public contracts (props, function signatures, events, routes,
  schemas) been touched without updating all their consumers within
  the diff?
- Are there implicit side-effects (execution order, shared state
  mutation, cache invalidation) that the diff does not make evident
  but could break in production?
- If you detect risk: name which existing test covers it, or describe
  what test is missing to protect it.

### 2) CORRECTNESS

- New bugs, uncovered edge cases, or side-effects outside the diff?
- Are TypeScript types coherent? Any unnecessary `any` or unsafe
  `as X` assertions?
- Is error handling complete and consistent with the rest of the module?

### 3) TESTS

- Do the tests follow the repository patterns (structure, helpers,
  factories, naming)? Flag concrete deviations, not style preferences.
- Do they cover the happy path, relevant errors, and real edge cases?
  Mark as noise any test that verifies trivial language or framework
  behaviour, or that duplicates an existing test.
- Do tests verify observable behaviour rather than coupling to
  implementation details (internal calls, intermediate render states)?
- Is there new behaviour without a test when the project requires
  coverage for that type of change?

### 4) LEAN CODE

- **Dead code**: functions, variables, imports, branches, or types
  never invoked within the diff or module scope. List each one.
- **Overengineering**: abstractions, layers, or patterns introduced
  for a single current use. Could the same be achieved with less
  indirection without breaking module consistency?
- **Scope creep**: code outside the task scope that adds surface
  without justification.
- **Bad practices**: omitted early returns, unnecessary nesting,
  magic numbers without names, functions with more than one clear
  responsibility, effects in unexpected places.

### 5) CONSISTENCY

- Names, patterns, and style aligned with the nearest neighbouring
  module?
- Imports, error handling, and validation in the same style as the
  rest of the feature?
- Layers respected (server / client, domain / application /
  infrastructure)?

---

Close with a single line: `PASS` or `NEEDS CHANGES`.

Then a prioritised list with severity:
- 🔴 Blocks merge (broken behaviour, real bug, missing critical test,
  confirmed dead code)
- 🟡 Should fix before merge (broken consistency, clear overengineering,
  weak test)
- ⚪ Optional / nitpick (minor style, future improvement)

If `PASS`: write "no blockers".
```

---

### `.claude/commands/commits.md`

```markdown
Review the current branch changes and propose how to commit them to
open a PR.

First, run:
```bash
git rev-parse --abbrev-ref HEAD
git status --porcelain
git diff main...HEAD
```

Apply the conventions from @docs/conventions/git-github-workflow.md.

Summary of required conventions:
- Branch commits: imperative, American English, no period, no
  `feat:`/`fix:`/`chore:` prefixes, no task IDs.
- PR title: Conventional Commits without scope (`feat:`, `fix:`, or
  `chore:`), no task IDs.
- Atomic commits — consequent changes go in the same commit.
- Optional body: 50/72 rule, explain what and why.
- Use explicit files in `git add`. Never `.`, `-A`, `-u`, or directories.
- Do not include `git push`, `git rebase`, `git merge`, or
  `gh pr create` unless explicitly asked.

Deliver in this exact order:

**1. Commit table**

| # | Message | Files |
|---|---------|-------|

**2. Commit order**
List commits from most foundational to most dependent.

**3. Suggested PR title**
One line: `feat:` / `fix:` / `chore:` — no scope.

**4. PR body (brief)**
- Why: what problem this solves
- Scope: what changed and what did not
- Technical approach: key decisions made
- How to test: steps to verify manually

**5. Bash block**

```bash
# Pre-checks
git rev-parse --abbrev-ref HEAD
git status --porcelain

# Commits (in the order from point 2)
git add <explicit files>
git commit -m "<exact message from table>"
# repeat per commit

git status
```

The sum of files across all `git add` commands must equal
`git status --porcelain`. If you intentionally exclude a file,
add a comment explaining why.
```

---

### `.github/pull_request_template.md`

```markdown
## Linear task

<!-- Link to the Linear task this PR resolves. -->
<!-- Example: Closes LIN-42 -->

## Summary

<!-- What changed and why. Two to four sentences. -->

## How to test

<!-- Steps to verify the acceptance criteria manually. -->

1.
2.
3.

## Out of scope

<!-- What this PR explicitly does NOT do. -->

## Checklist

- [ ] Acceptance criteria from the task are met
- [ ] Plan document committed in `docs/plans/` (skip for spikes)
- [ ] ADR added in `docs/adr/` (only for architecture changes)
- [ ] `npm run typecheck && npm run lint && npm run test` pass locally
- [ ] No `.env*` files committed
```

---

## Step 3 — Verify before committing

Run these checks and confirm all pass:

```bash
# Confirm you are on the right branch
git rev-parse --abbrev-ref HEAD
# expected: setup/001-foundation

# Confirm dev server still works
npm run dev
# open localhost:3000, confirm the page loads, then stop with Ctrl+C

# Confirm placeholder scripts exit 0
npm run typecheck
npm run test

# Confirm .env.local is ignored
echo "ANTHROPIC_API_KEY=test" > .env.local
git status
# .env.local must NOT appear in the output
rm .env.local
```

---

## Step 4 — Commit and push

Use the `/commits` slash command to generate the bash block for this PR.

Reference commit structure if you need it:

```bash
# Pre-checks
git rev-parse --abbrev-ref HEAD
git status --porcelain

# Commit 1 — repo metadata
git add LICENSE .nvmrc .gitignore .env.example README.md
git commit -m "Add repo metadata: license, node version, gitignore, env example"

# Commit 2 — human-agent contract
git add AGENTS.md CLAUDE.md ARCHITECTURE.md
git commit -m "Add human-agent contract: AGENTS.md, CLAUDE.md, ARCHITECTURE.md"

# Commit 3 — docs skeleton
git add docs/conventions/README.md docs/conventions/git-github-workflow.md
git add docs/plans/README.md docs/plans/SETUP-001.md
git add docs/adr/README.md docs/adr/0001-record-architecture-decisions.md
git add docs/templates/linear-task.md docs/templates/linear-project.md
git commit -m "Add docs skeleton: conventions, plans, ADRs, templates"

# Commit 4 — Claude Code assets
git add .claude/commands/review.md .claude/commands/commits.md
git commit -m "Add Claude Code slash commands: /review and /commits"

# Commit 5 — GitHub conventions
git add .github/pull_request_template.md
git commit -m "Add GitHub PR template"

# Commit 6 — package.json scripts and engines
git add package.json
git commit -m "Add canonical placeholder scripts and node engines to package.json"

git status
```

Then push:

```bash
git push -u origin setup/001-foundation
```

---

## Step 5 — Open the PR on GitHub

1. Go to `github.com/isaias1984/chat-streaming-claude`.
2. Click the **Compare & pull request** banner that appears after the push.
3. The PR template pre-fills the description. Fill in:
   - Linear task link (if the task exists yet in Linear).
   - How to test section with the verification steps from Step 3.
4. Submit the PR. CodeRabbit will comment within a few minutes.
5. Read the comments. Fix any 🔴 blockers on the same branch and push —
   CodeRabbit re-reviews automatically.
6. When satisfied, merge using **Squash and merge**. The PR title becomes
   the commit on `main`.
7. Close the Linear task.

---

## What comes next

- **SETUP-002** — Quality tooling: TypeScript strict mode, ESLint flat
  config, Prettier, Vitest with at least one smoke test. After this PR,
  all canonical commands are real.
- **SETUP-003** — Automation: Husky + lint-staged, commitlint, GitHub
  Actions CI, `.coderabbit.yaml`, branch protection updated to require
  status checks.
- **TASK-001** — The actual chat implementation. First PR that exercises
  the full flow end-to-end.
