# AGENTS

- Purpose: shared rules for agent policy/micro-fixes in this repo.

## Scope

- Applies to the directory tree rooted here; deeper AGENTS.md files take precedence.

## Principles

- Priorities: accuracy, minimal diffs, consistency, concision (in this order).
- Do not change policy substance; clarify wording only.
- Use RFC 2119 terms (MUST/SHOULD/MAY) where unambiguous.

## I/O Contract

- Input: explicit target files, goals, and constraints.
- Output: only minimal diffs to target files; no meta-comments; preserve existing formatting rules in tasks.
- Testability: add rules as 1–2 line, verifiable sentences when needed.

## Authority Boundaries

- Tools: read/write within the workspace; no external network unless allowed by the harness.
- Escalation: request human approval for destructive or out‑of‑scope actions.

## Fail-safe / Escalation

- Stop and ask a human when requirements conflict, edits exceed per‑file limits, or creating missing targets would breach minimality.
- If a command failure risks unintended changes, abort and request review.

## Notes

- If `.cursor/rules` exists, treat this file as the source of truth and keep `.cursor/rules` aligned and minimal.

