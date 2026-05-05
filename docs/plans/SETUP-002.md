# SETUP-002 — Execution context for Claude Code

## What you are and what to do

You are Claude Code running inside VSCode on the repository
`chat-streaming-claude` (github.com/isaias1984/chat-streaming-claude).

SETUP-001 declared five canonical commands as placeholder stubs. Your task
is to wire them to real tools by adding an ESLint flat config, Prettier
config, and Vitest setup. Three new devDependencies are needed: `vitest`,
`prettier`, and `@eslint/eslintrc`. ESLint v9 and `eslint-config-next` are
already installed.

**`tsconfig.json` does not need changes** — it already has `"strict": true`
and `"noEmit": true`. Only `package.json`, `AGENTS.md`, and five new files
are touched.

**Do not modify any file unless the instructions below explicitly say so.**

When all files are created and modified, stop and wait. Do not commit
anything yet — the commit step is at the end and uses the `/commits` slash
command.

---

## Step 1 — Create the branch

```bash
git checkout -b setup/002-quality-tooling
```

---

## Step 2 — Create these files

### `eslint.config.mjs`

```js
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const compat = new FlatCompat({ baseDirectory: __dirname });

export default [
  { ignores: [".next/", "node_modules/"] },
  ...compat.extends("next/core-web-vitals", "next/typescript"),
];
```

### `.prettierrc`

```json
{
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "all",
  "semi": true
}
```

### `vitest.config.ts`

```ts
import { defineConfig } from "vitest/config";
import path from "path";
import { fileURLToPath } from "url";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src"),
    },
  },
  test: {
    environment: "node",
  },
});
```

### `src/lib/utils.ts`

```ts
export function isNonEmptyString(value: unknown): value is string {
  return typeof value === "string" && value.trim().length > 0;
}
```

### `src/__tests__/smoke.test.ts`

```ts
import { describe, expect, it } from "vitest";
import { isNonEmptyString } from "@/lib/utils";

describe("isNonEmptyString", () => {
  it("returns true for a non-empty string", () => {
    expect(isNonEmptyString("hello")).toBe(true);
  });

  it("returns false for an empty string", () => {
    expect(isNonEmptyString("")).toBe(false);
  });

  it("returns false for a whitespace-only string", () => {
    expect(isNonEmptyString("   ")).toBe(false);
  });

  it("returns false for non-string values", () => {
    expect(isNonEmptyString(null)).toBe(false);
    expect(isNonEmptyString(42)).toBe(false);
    expect(isNonEmptyString(undefined)).toBe(false);
  });
});
```

---

## Step 3 — Modify these files

### `package.json`

Add three entries to `devDependencies` (alongside existing ones):

```json
"@eslint/eslintrc": "^3",
"prettier": "^3",
"vitest": "^3"
```

Replace the five script entries:

```json
"lint": "eslint .",
"typecheck": "tsc --noEmit",
"format": "prettier --write .",
"test": "vitest run",
"test:watch": "vitest"
```

Note: `lint` changes from `next lint` to `eslint .` — `next lint` does not
reliably pick up `eslint.config.mjs` in all Next.js versions. `eslint .`
uses the flat config directly.

After saving `package.json`, run:

```bash
npm install
```

### `AGENTS.md` — remove ⚠️ markers from the canonical commands table

Replace these five rows in the table:

| `npm run typecheck`  | TypeScript type check ⚠️ wired in SETUP-002 |
| `npm run lint`       | ESLint check ⚠️ wired in SETUP-002          |
| `npm run format`     | Prettier format ⚠️ wired in SETUP-002       |
| `npm run test`       | Run unit tests ⚠️ wired in SETUP-002        |
| `npm run test:watch` | Tests in watch mode ⚠️ wired in SETUP-002   |

With:

| `npm run typecheck`  | TypeScript type check |
| `npm run lint`       | ESLint check          |
| `npm run format`     | Prettier format       |
| `npm run test`       | Run unit tests        |
| `npm run test:watch` | Tests in watch mode   |

Also remove this line from the **Known asymmetries and debt** section:

> `typecheck`, `lint`, `format`, `test`, and `test:watch` are declared
> but not wired. They will be implemented in SETUP-002.

---

## Step 4 — Verify before committing

```bash
# Confirm you are on the right branch
git rev-parse --abbrev-ref HEAD
# expected: setup/002-quality-tooling

# TypeScript — no errors expected (strict was already on; scaffold is clean)
npm run typecheck

# ESLint — no errors expected on a fresh scaffold
npm run lint

# Prettier — run it; check if any scaffold files were reformatted
npm run format
git status
# If layout.tsx, page.tsx, or globals.css appear modified, that is expected.
# Commit them as part of this PR (see Step 5 commit 7).

# Tests — smoke test must pass
npm run test

# Full pre-PR gate
npm run typecheck && npm run lint && npm run test
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
git add docs/plans/SETUP-002.md
git commit -m "Add SETUP-002 plan doc"

# Commit 2 — wire scripts and add devDependencies
git add package.json package-lock.json
git commit -m "Wire quality scripts and add vitest, prettier, @eslint/eslintrc"

# Commit 3 — ESLint flat config
git add eslint.config.mjs
git commit -m "Add ESLint flat config"

# Commit 4 — Prettier config
git add .prettierrc
git commit -m "Add Prettier config"

# Commit 5 — Vitest config and smoke test
git add vitest.config.ts src/lib/utils.ts src/__tests__/smoke.test.ts
git commit -m "Add Vitest config and smoke test for isNonEmptyString"

# Commit 6 — AGENTS.md cleanup
git add AGENTS.md
git commit -m "Remove SETUP-002 placeholder markers from AGENTS.md"

# Commit 7 — Prettier-reformatted scaffold files (only if format changed them)
# git add src/app/layout.tsx src/app/page.tsx src/app/globals.css
# git commit -m "Apply Prettier formatting to scaffold files"

git status
```

Then push:

```bash
git push -u origin setup/002-quality-tooling
```

---

## Step 6 — Open the PR on GitHub

1. Go to `github.com/isaias1984/chat-streaming-claude`.
2. Click the **Compare & pull request** banner.
3. Fill in the PR template:
   - Linear task link (if it exists).
   - How to test: the verification commands from Step 4.
4. Submit the PR. CodeRabbit will comment within a few minutes.
5. Fix any 🔴 blockers and push — CodeRabbit re-reviews automatically.
6. Merge with **Squash and merge**.
   Suggested PR title: `chore: add quality tooling — ESLint, Prettier, Vitest`
7. Close the Linear task.

---

## What comes next

- **SETUP-003** — Automation: Husky + lint-staged, commitlint, GitHub
  Actions CI, `.coderabbit.yaml`, branch protection updated to require
  status checks, CHANGELOG.md.
- **TASK-001** — The actual chat implementation. First PR that exercises
  the full workflow end-to-end.
