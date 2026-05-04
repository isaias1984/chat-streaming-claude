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
