# Design: `refactor` schema

- **Date**: 2026-06-19
- **Status**: Implemented (`openspec schema validate refactor` passes)
- **Relation**: fourth schema. Behavior-preserving structural change.

## Purpose

`refactor` changes the structure of existing code **without changing observable
behavior**. It encodes agent-skills `code-simplification`'s first principle
("preserve behavior exactly") as workflow structure.

## Key principles baked in

- **Safety-net first** — `safety-net` (characterization tests pinning current
  behavior) is `requires` of `target` and `tasks`, so you cannot design or
  execute the refactor before the net exists.
- **Behavior preserved exactly** — every step must keep the safety-net tests
  green *without modifying them*; the `apply` tight loop reverts any step that
  turns a test red (a red test means behavior changed).
- **Incremental** — small steps, commit often.
- **Wide loop = wrong change-shape** — if the work reveals behavior *should*
  change, that is a `feature` or `bugfix`, not a refactor; split it out.

## Artifact chain (4 + apply)

| # | artifact | role | requires |
|---|---|---|---|
| 1 | `proposal` | why; structural change; behavior that must stay identical; scope | — |
| 2 | `safety-net` | existing coverage, gaps, characterization tests, green baseline | proposal |
| 3 | `target` | current → target structure; behavior-preserving moves; incremental sequence | proposal, safety-net |
| 4 | `tasks` | incremental steps (change → tests green → commit) + verification | target, safety-net |
| — | `apply` | execute incrementally; tight loop reverts on red; wide loop splits out | tasks |

Inherits constitution/quality via openspec config; reuses the two-radius `apply`
loop and the progressive-interviewing protocol.
