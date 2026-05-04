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
