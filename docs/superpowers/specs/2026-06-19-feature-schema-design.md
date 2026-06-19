# Design: `feature` schema

- **Date**: 2026-06-19
- **Status**: Implemented (`openspec schema validate feature` passes)
- **Relation**: second schema in the library; complements
  [`greenfield-bootstrap`](2026-06-19-openspec-schemas-greenfield-design.md).

## Purpose

`feature` is the change-shape for **adding a capability to an existing system**.
It reuses the same set of concern artifacts as `greenfield-bootstrap`, but each
is framed as a **delta to the current baseline** rather than built from scratch.

## Key decision: same concerns, delta-framed (not folded)

The library principle is *granularity = change shape; concerns = artifacts
inside a schema*. A feature touches the same concerns as greenfield (API, data,
observability, security, tests), so those stay **first-class artifacts** — just
delta-oriented ("describe the change; if untouched, state 'no change because…'",
which is the OpenSpec thin-artifact skip). Folding them into `design`
sub-sections was rejected as inconsistent with the principle and as hiding
shift-left concerns.

What genuinely differs from greenfield is **from-scratch vs delta**, so the
project-bootstrap-only artifacts drop out (a feature inherits them):

| Dropped artifact | Why | Where it lives instead |
|---|---|---|
| `product-brief` | no new product vision | replaced by `proposal` |
| `constitution` | set once per project | inherited via openspec config `context` |
| `quality-strategy` | test *strategy* is project-level | inherited; test *cases* come from `specs` scenarios + `acceptance-and-review` + TDD `tasks` |
| `project-scaffold` | repo already exists | — |
| `launch-readiness` | per-feature ship = flag/rollout | folded into `design` Rollout; repeated release = future `release` schema |

## Artifact chain (9 + apply)

| # | artifact | generates | requires |
|---|---|---|---|
| 1 | `proposal` | proposal.md | — |
| 2 | `specs` | specs/**/*.md (ADDED/MODIFIED/REMOVED, EARS + scenarios) | proposal |
| 3 | `design` | design.md (approach, ADRs, architecture impact, rollout/flag) | proposal, specs |
| 4 | `api-contract` | api-contract.md (delta) | design |
| 5 | `data-model` | data-model.md (delta) | design |
| 6 | `observability` | observability.md (delta) | design |
| 7 | `security-baseline` | security-baseline.md (delta) | design |
| 8 | `acceptance-and-review` | acceptance-and-review.md | specs |
| 9 | `tasks` | tasks.md (TDD + Verification + Review) | design, 4, 5, 6, 7, 8 |
| — | `apply` | tracks tasks.md | tasks |

Reused from greenfield: the EARS spec deltas, the concern artifacts, the
two-radius `apply` feedback loop, and the progressive-interviewing **Gathering
inputs** protocol (proposal/specs = primary interview; the rest lean on
dependencies and the existing system). See greenfield design §6 and §11.

## Non-goals

- Repeated production releases — future `release` schema.
- Brownfield-wide refactors — future `refactor` schema.
