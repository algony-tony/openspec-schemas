# Design: `release` schema

- **Date**: 2026-06-19
- **Status**: Implemented (`openspec schema validate release` passes)
- **Relation**: fifth schema. Ship-to-production change-shape; the operational
  companion to `greenfield-bootstrap`'s `launch-readiness` (which handles only
  the first launch).

## Purpose

`release` ships an existing system to production safely and repeatedly. It
encodes agent-skills `shipping-and-launch` (reversible, observable, incremental;
pre-launch checklist), git-workflow versioning, and gstack `land-and-deploy`.

## Key principles baked in

- **Go/no-go gates** — `preflight` is an explicit checklist; a failed gate is a
  no-go. It `requires` `release-plan`, which `requires` `scope`.
- **Reversible & incremental** — `release-plan` mandates a rollback plan with
  triggers and a staged/canary rollout; `tasks` is a runbook with rollback
  points.
- **Halt-and-rollback** — the `apply` tight loop stops and rolls back on any
  failing gate/stage rather than pushing forward.
- **Route defects out** — the wide loop sends a discovered defect to a `bugfix`
  change and a missing capability to a `feature`.

## Artifact chain (4 + apply)

| # | artifact | role | requires |
|---|---|---|---|
| 1 | `scope` | version, included changes, changelog, audience & risk | — |
| 2 | `release-plan` | versioning, migration ordering, rollout strategy, rollback, comms | scope |
| 3 | `preflight` | go/no-go readiness gates + sign-off | release-plan |
| 4 | `tasks` | rollout runbook (Cut → Stage → Roll out → Verify → Retro) | preflight |
| — | `apply` | execute rollout; halt+rollback on failure; verify + comms | tasks |

Inherits constitution/quality via openspec config; reuses the two-radius `apply`
loop and the progressive-interviewing protocol.
