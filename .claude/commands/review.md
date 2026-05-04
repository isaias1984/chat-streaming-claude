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
