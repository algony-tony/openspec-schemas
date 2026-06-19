# openspec-schemas

A reusable, MIT-licensed library of custom [OpenSpec](https://github.com/Fission-AI/OpenSpec)
workflow schemas covering full-stack software development — plus auxiliary
workflows. Schemas are technology-stack agnostic.

> **Status:** the flagship schema `greenfield-bootstrap` is **available** at
> `openspec/schemas/greenfield-bootstrap/` (`openspec schema validate` passes).
> See the design doc:
> [`docs/superpowers/specs/2026-06-19-openspec-schemas-greenfield-design.md`](docs/superpowers/specs/2026-06-19-openspec-schemas-greenfield-design.md)
> and the implementation plan:
> [`docs/superpowers/plans/2026-06-19-greenfield-bootstrap-schema.md`](docs/superpowers/plans/2026-06-19-greenfield-bootstrap-schema.md).

## What is a schema?

In OpenSpec, a **schema** is a development *workflow*: an ordered chain of
artifacts (each with a template + an instruction that guides the AI) plus an
`apply` phase. A project picks one schema per change. This library provides
schemas tuned to different **change shapes**.

## Planned schemas

| Schema | Shape | Status |
|---|---|---|
| `greenfield-bootstrap` | New full-stack project → verified, launch-ready MVP | ✅ Available |
| `feature` | Add a capability to an existing system | Planned |
| `bugfix` | Reproduce → root cause → fix → regression | Planned |
| `refactor`, `release`, … | Various | Planned |

### `greenfield-bootstrap` at a glance

Covers `DEFINE → PLAN → BUILD → VERIFY → REVIEW → SHIP` with an explicit
verification/review feedback loop. Artifacts: product-brief, constitution,
requirements, architecture, api-contract, data-model, quality-strategy,
observability, security-baseline, project-scaffold, acceptance-and-review,
tasks, launch-readiness.

## Recommended tooling (optional)

Schemas reference capabilities tool-agnostically. For browser-based
verification, a good concrete option is gstack [`browse`](https://github.com/garrytan/gstack)
(headless Chromium CLI). Install and run only with the user's consent.

## Credits & license

MIT — see [`LICENSE`](LICENSE). This project synthesizes patterns from several
MIT-licensed projects; attribution and notices are in [`CREDITS.md`](CREDITS.md).
