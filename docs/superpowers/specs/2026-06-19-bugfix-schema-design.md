# Design: `bugfix` schema

- **Date**: 2026-06-19
- **Status**: Implemented (`openspec schema validate bugfix` passes)
- **Relation**: third schema in the library. Debugging-first change-shape.

## Purpose

`bugfix` fixes a defect in an existing system. It encodes the
`superpowers:systematic-debugging` discipline (also seen in agent-skills
`debugging-and-error-recovery` and gstack `investigate`) directly into the
artifact chain, so the workflow cannot skip root-cause analysis.

## Key principles baked in

- **The Iron Law** — *no fix without root-cause investigation first*. Enforced
  structurally: `fix` `requires` `root-cause`, so the fix artifact is blocked
  until the root cause exists. The `root-cause` instruction forbids advancing
  until the cause is confirmed with evidence.
- **Preserve evidence / reproduce first** — `report` captures verbatim evidence
  and a reliable repro before anyone investigates.
- **Regression-test-first** — `tasks` phase 1 is "write a failing regression
  test that reproduces the bug"; the fix follows (red → green), guarding against
  recurrence.
- **3+ failed fixes → question the architecture** — in the `apply` tight loop.
- **Wide loop for spec gaps** — if the root cause is that a requirement/spec is
  wrong, amend the spec as a follow-up change rather than patching around it.

## Artifact chain (4 + apply, strictly linear)

| # | artifact | systematic-debugging mapping | requires |
|---|---|---|---|
| 1 | `report` | capture: symptom, repro, evidence, environment, impact | — |
| 2 | `root-cause` | Phases 1–3: reproduce, recent changes, hypotheses, confirmed cause | report |
| 3 | `fix` | Phase 4 plan: root-cause fix, regression test, blast radius, spec impact | root-cause |
| 4 | `tasks` | TDD: failing regression test → fix → verify symptom gone | fix |
| — | `apply` | execute + verify; tight loop (re-investigate / question architecture); wide loop (amend spec) | tasks |

No concern artifacts (api/data/observability): a bugfix introduces no new
concerns — if it changes the API/data surface, that is a `feature`, not a fix.

## Reuse

Inherits the project constitution/quality via openspec config; reuses the
two-radius `apply` loop and the progressive-interviewing **Gathering inputs**
protocol from the other schemas (`report` = primary capture; the rest read prior
artifacts and the code).
