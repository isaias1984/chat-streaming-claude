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
