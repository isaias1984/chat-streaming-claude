# SETUP-003 — Execution context for Claude Code

## What you are and what to do

You are Claude Code running inside VSCode on the repository
`chat-streaming-claude` (github.com/isaias1984/chat-streaming-claude).

SETUP-002 wired all five canonical commands to real tools. Your task is to
add the automation layer: commit-time quality checks (Husky + lint-staged +
commitlint), a GitHub Actions CI pipeline, CodeRabbit configuration, and
an initial CHANGELOG.

**Do not modify any file unless the instructions below explicitly say so.**

When all files are created and modified, stop and wait. Do not commit
anything yet — the commit step is at the end and uses the `/commits` slash
command.

---

## Step 1 — Create the branch

```bash
git checkout -b setup/003-automation
```

---

## Step 2 — Create these files

### `.husky/pre-commit`

```sh
npx lint-staged
```

### `.husky/commit-msg`

```sh
npx --no -- commitlint --edit $1
```

### `.commitlintrc.json`

The project uses non-prefixed commits on feature branches (see
`docs/conventions/git-github-workflow.md`). Conventional commit prefixes
(`feat:`, `fix:`, `chore:`) are reserved for the squash commit on `main`.

To avoid blocking valid branch commits, this config enforces only structural
hygiene rules — length and whitespace — without requiring a type prefix.

The `subject-empty` rule is intentionally omitted: commitlint parses plain
non-prefixed messages (e.g. `Add streaming route handler`) as `type` with an
empty `subject`, which would incorrectly reject every branch commit. Git
itself prevents truly empty commit messages.

```json
{
  "$schema": "https://json.schemastore.org/commitlintrc",
  "rules": {
    "header-max-length": [2, "always", 72],
    "header-trim": [2, "always"],
    "subject-case": [0]
  }
}
```

### `.github/workflows/ci.yml`

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  quality:
    name: Type check, lint, and test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version-file: .nvmrc
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Type check
        run: npm run typecheck

      - name: Lint
        run: npm run lint

      - name: Test
        run: npm run test
```

### `.coderabbit.yaml`

```yaml
# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
language: "en-US"
early_access: false
reviews:
  profile: "chill"
  request_changes_workflow: false
  high_level_summary: true
  poem: false
  review_status: true
  auto_review:
    enabled: true
    drafts: false
chat:
  auto_reply: true
```

### `CHANGELOG.md`

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]
```

---

## Step 3 — Modify these files

### `package.json`

Add three entries to `devDependencies` (alongside existing ones):

```json
"@commitlint/cli": "^19",
"husky": "^9",
"lint-staged": "^15"
```

Add a `prepare` script entry alongside the existing scripts:

```json
"prepare": "husky"
```

Add a top-level `lint-staged` configuration object (alongside `devDependencies`,
`scripts`, etc.):

```json
"lint-staged": {
  "*.{ts,tsx,js,mjs}": ["prettier --write", "eslint"],
  "*.{json,md,css}": "prettier --write"
}
```

After saving `package.json`, run:

```bash
npm install
```

This installs the new packages and runs `husky` via the `prepare` script,
which registers `.husky/` as the git hooks directory. The two hook files
you created in Step 2 will be made executable automatically.

### `AGENTS.md`

**Workflow modules table** — remove the `(added in SETUP-003)` suffixes from
the CodeRabbit and CI rows.

Replace:

```
| AI code review   | CodeRabbit           | `.coderabbit.yaml` (added in SETUP-003)   |
| CI/CD            | GitHub Actions       | `.github/workflows/` (added in SETUP-003) |
```

With:

```
| AI code review   | CodeRabbit           | `.coderabbit.yaml`                        |
| CI/CD            | GitHub Actions       | `.github/workflows/`                      |
```

**Repository layout** — remove the SETUP-003 note from the workflows line,
add `.husky/` block, and add `CHANGELOG.md` to the top-level file list.

Replace:

```
  workflows/              ← CI/CD pipelines (wired in SETUP-003)
```

With:

```
  workflows/              ← CI/CD pipelines
```

Add a `.husky/` block immediately before `.github/`:

```
.husky/
  pre-commit              ← runs lint-staged on staged files
  commit-msg              ← runs commitlint on commit messages
```

Add `CHANGELOG.md` to the top-level file list, after `ARCHITECTURE.md`:

```
CHANGELOG.md              ← project changelog (Keep a Changelog format)
```

---

## Step 4 — Verify before committing

```bash
# Confirm you are on the right branch
git rev-parse --abbrev-ref HEAD
# expected: setup/003-automation

# Full quality gate
npm run typecheck && npm run lint && npm run test

# Confirm hook files exist
ls .husky/
# expected: pre-commit  commit-msg

# Confirm Husky registered the hooks directory with git
git config core.hooksPath
# expected: .husky
```

---

## Step 5 — Commit and push

Use the `/commits` slash command to generate the bash block for this PR.

Reference commit structure if you need it:

```bash
# Pre-checks
git rev-parse --abbrev-ref HEAD
git status --porcelain

# Commit 1 — plan doc (foundational; committed before code)
git add docs/plans/SETUP-003.md
git commit -m "Add SETUP-003 plan doc"

# Commit 2 — GitHub Actions CI
git add .github/workflows/ci.yml
git commit -m "Add GitHub Actions CI workflow"

# Commit 3 — CodeRabbit config
git add .coderabbit.yaml
git commit -m "Add CodeRabbit config"

# Commit 4 — Husky + lint-staged + commitlint
git add package.json package-lock.json .husky/pre-commit .husky/commit-msg .commitlintrc.json
git commit -m "Add Husky hooks, lint-staged, and commitlint"

# Commit 5 — CHANGELOG
git add CHANGELOG.md
git commit -m "Add initial CHANGELOG"

# Commit 6 — AGENTS.md cleanup
git add AGENTS.md
git commit -m "Remove SETUP-003 placeholder markers from AGENTS.md"

git status
```

Then push:

```bash
git push -u origin setup/003-automation
```

---

## Step 6 — Open the PR on GitHub and configure branch protection

### Open the PR

1. Go to `github.com/isaias1984/chat-streaming-claude`.
2. Click the **Compare & pull request** banner.
3. Fill in the PR template:
   - Linear task link (if it exists).
   - How to test: the verification commands from Step 4.
4. Submit the PR. CodeRabbit will comment within a few minutes.
5. Fix any 🔴 blockers and push — CodeRabbit re-reviews automatically.
6. Merge with **Squash and merge**.
   Suggested PR title: `chore: add automation — Husky, lint-staged, commitlint, CI`
7. Close the Linear task.

### Configure branch protection (manual step, after merge)

After the CI workflow exists on `main`:

1. Go to `github.com/isaias1984/chat-streaming-claude` → **Settings** →
   **Branches**.
2. Edit the protection rule for `main`.
3. Enable **Require status checks to pass before merging**.
4. Search for and add `quality` (the job name defined in `ci.yml`).
5. Enable **Require branches to be up to date before merging**.
6. Save.

---

## What comes next

- **TASK-001** — The actual chat implementation: streaming route handler
  (`src/app/api/chat/route.ts`) + `useChat` hook in `src/app/page.tsx`.
  First PR that exercises the full workflow end-to-end.
