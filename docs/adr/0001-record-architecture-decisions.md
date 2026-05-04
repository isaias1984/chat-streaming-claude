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
