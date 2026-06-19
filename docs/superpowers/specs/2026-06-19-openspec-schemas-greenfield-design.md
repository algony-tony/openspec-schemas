# Design: openspec-schemas — Library Architecture & `greenfield-bootstrap` Flagship

- **Date**: 2026-06-19
- **Status**: Approved (brainstorming complete, pending spec review)
- **Owner**: algony-tony

## 1. Context & Goals

`openspec-schemas` is a **reusable, MIT-licensed library of custom OpenSpec
workflow schemas** that cover full-stack software development end-to-end, plus
auxiliary workflows. The schemas are technology-stack agnostic so they can be
published and consumed by any project.

The first schema we build — the **flagship** — is `greenfield-bootstrap`: the
workflow for standing up a brand-new full-stack project from a fuzzy idea
through to a verified, launch-ready MVP. Authoring it forces us to write
canonical instruction content for every cross-cutting concern (API, data,
quality, observability, security), which then seeds every later schema.

**Success criteria**

- `greenfield-bootstrap` is a valid OpenSpec schema (`openspec schema validate`
  passes) that a fresh project can select and run end-to-end.
- Each artifact has a clear single purpose, a template, and an instruction
  grounded in a named reference.
- The workflow covers DEFINE → PLAN → BUILD → VERIFY → REVIEW → SHIP, including
  an explicit verification/review/feedback loop — not "stops when code is
  written."
- Library is organized so additional change-shape schemas (feature, bugfix,
  refactor, release, …) can be added with consistent conventions.

## 2. How OpenSpec schemas work (mechanics we rely on)

A schema is a directory: `schema.yaml` + `templates/*.md`. Verified against the
installed `@fission-ai/openspec` v1.4.1 source:

- **Artifact fields**: `id`, `generates`, `description`, `template`,
  `instruction?`, `requires[]` (default `[]`). There is **no `optional` flag and
  no `when`/`condition`** field.
- **Schema top-level**: `name`, `version`, `description?`, `artifacts[]`,
  `apply?`. There is **no `extends`/`include`/`mixin`** — schemas cannot inherit
  or share artifacts.
- **Apply phase**: `requires[]` (≥1), `tracks?` (a checkbox file), `instruction?`.
- **Config injection**: `openspec/config.yaml` `context` is *injected into every
  artifact instruction*; `rules` are injected per-artifact.

Two consequences shape the whole design:

1. **"Skip" = write a thin artifact.** Because there is no optional flag, an
   artifact that does not apply to a given change is satisfied by a short
   "N/A, because …" document. The built-in `spec-driven` `design` artifact
   already works this way.
2. **No cross-schema reuse.** Shared instruction content (e.g. API design
   guidance) must be maintained as **canonical instruction modules in this repo**
   and composed (copied/adapted) into each schema at authoring time.

## 3. Design principles

1. **Granularity = change shape, not concern.** One schema per *kind of work*
   (greenfield, feature, bugfix, refactor, release). Concerns (API, data, tests,
   observability, security) are **artifacts inside** a schema, never separate
   schemas — otherwise one feature fragments into many changes.
2. **Comprehensive but thin.** A schema includes every concern relevant to its
   shape; depth scales to the change's risk, and irrelevant artifacts become
   thin documents.
3. **Shift-left.** API contract, data model, testability, observability, and
   security are first-class artifacts from day 0 — never bolted on after MVP.
4. **Verify/Review/Loop are first-class**, not an afterthought (see §6).
5. **Tool-agnostic.** Instructions reference *capabilities* (e.g. "a browser
   automation tool"), not specific implementations. Concrete recommendations
   live in an appendix.
6. **Constitution → config.** A project's principles, boundaries, and tech-stack
   are captured once and injected into all future changes via OpenSpec config.

## 4. Library architecture

```
openspec-schemas/
  openspec/
    config.yaml
    schemas/
      greenfield-bootstrap/
        schema.yaml
        templates/*.md
      <future-schema>/...
  docs/superpowers/{specs,plans}/   # design docs & implementation plans
  CREDITS.md / NOTICE               # attribution
  README.md
  CLAUDE.md
```

Schemas live under **`openspec/schemas/<name>/`** because OpenSpec resolves
project-local schemas from `<projectRoot>/openspec/schemas/<name>/schema.yaml`
(verified in v1.4.1). Storing them there lets this repo dogfood/validate its own
schemas and makes the directory double as the distribution unit. A shared
`modules/` directory for reusable instruction blocks is **deferred until a
second schema needs reuse** (YAGNI).

**Distribution** (resolution order: project-local
`<root>/openspec/schemas/<name>` → user override
`${XDG_DATA_HOME}/openspec/schemas/<name>` → package built-in). A consumer
copies `openspec/schemas/<name>/` into their own project's `openspec/schemas/`,
or into the user-level override dir. Packaging is a later concern.

## 5. Flagship: `greenfield-bootstrap` artifact chain

14 artifacts + an `apply` engine. `requires` shown is indicative; the exact DAG
is finalized in the `schema.yaml`.

| Segment | # | Artifact | Purpose | Primary source | requires |
|---|---|---|---|---|---|
| DEFINE | 1 | `product-brief` | Vision, personas, MVP scope, success metrics, non-goals | BMAD Brief / Agent OS mission | — |
| DEFINE | 2 | `constitution` | Principles + three-tier boundaries (Always/Ask-first/Never) + tech-stack; **writes condensed copy into `openspec/config.yaml` context** | spec-kit constitution / Agent OS standards / agent-skills boundaries | product-brief |
| DEFINE | 3 | `specs` | Capability specs — ADDED requirements in EARS + `#### Scenario`; archived into `openspec/specs/` so `feature` has a baseline | Kiro EARS / BMAD PRD / spec-driven | product-brief, constitution |
| PLAN | 4 | `architecture` | System context, component boundaries (C4), key decisions as ADRs, runtime topology, -ilities targets | BMAD ADR / arc42 / C4 / Well-Architected | specs, constitution |
| PLAN | 5 | `api-contract` | Contract-first interfaces, error model, versioning, deprecation (Hyrum's Law) | agent-skills api-and-interface-design | architecture |
| PLAN | 6 | `data-model` | Entities/ERD, storage choice, migration & seed, data lifecycle/retention | — | architecture |
| PLAN | 7 | `quality-strategy` | Test pyramid, testability seams, coverage gates, **TDD as default discipline** | agent-skills TDD / superpowers TDD | architecture |
| PLAN | 8 | `observability` | "On-call three questions" → logs/metrics/traces, health checks, SLO/alerts | agent-skills observability-and-instrumentation | architecture |
| PLAN | 9 | `security-baseline` | Trust boundaries, STRIDE, OWASP always-do, abuse cases, data classification | agent-skills security-and-hardening / gstack cso | architecture |
| PLAN | 10 | `project-scaffold` | Repo layout, tooling, config/env (12-factor), CI/CD, IaC, local dev | agent-skills ci-cd / 12-factor | architecture |
| PLAN | 11 | `ux` | Screens/flows, component inventory, design tokens, accessibility; **imports a prototype (dir/archive/mockup) if provided**; thin when no UI | agent-skills frontend-ui-engineering / gstack design-* | architecture |
| VERIFY bar | 12 | `acceptance-and-review` | Per-requirement acceptance criteria, Definition of Done, code-review checklist, **requirements↔code traceability plan** | spec-kit /analyze / gstack reviews / superpowers code-review | specs, quality-strategy |
| BUILD | 13 | `tasks` | TDD per build task (red/green/commit) + final `## Verification` and `## Review` phases | superpowers writing-plans / Kiro waves | architecture, api-contract, data-model, quality-strategy, observability, security-baseline, ux, acceptance-and-review, project-scaffold |
| SHIP | 14 | `launch-readiness` | Pre-launch checklist: CI green, observability wired, security pass, rollback/runbook, staging smoke | agent-skills shipping-and-launch / gstack land-and-deploy | observability, security-baseline, project-scaffold, acceptance-and-review |

`apply`: `requires: [tasks, launch-readiness]`, `tracks: tasks.md`, with the
feedback-loop instruction from §6.

## 6. Verification, review & the feedback loop (the "back half")

The workflow must not end when code is written. Three mechanisms:

**A — Set the bar upfront.** `quality-strategy` (#7) fixes test levels + TDD;
`acceptance-and-review` (#11) fixes per-requirement acceptance criteria, the
Definition of Done, the code-review checklist, and a requirements↔code
traceability plan — all *before* coding.

**B — Bake verify/review into execution.** `tasks` (#12) uses TDD per build
task and ends with explicit `## Verification` (full suite, lint, typecheck,
build, observability/security smoke) and `## Review` (code review against the
checklist + requirements traceability). `apply` runs these as gates.

> **Dogfooding fix (2026-06-19).** A first real run produced an app that 404'd on
> startup while every test box was checked, plus a `tasks.md` written twice. Root
> causes + fixes folded back into the schema: (1) **a green test suite is not
> proof the app works** — `Verification` now requires *running the app the way a
> user does, opening its entry URL, and confirming a non-404 working page + one
> core flow*, with real evidence; (2) **no coherent run/entry model** — the
> `project-scaffold` `Run & Entry` section now requires one run command, the
> single entry URL, and how FE/BE connect (an API-only backend whose root 404s is
> not an entry); (3) `acceptance-and-review` now requires an **end-to-end,
> user-facing** criterion; (4) `apply` must update `tasks.md` **in place** (one
> task list, never a duplicated rewrite). These apply to `feature` too, and the
> in-place rule to all schemas.

**C — The feedback loop (two radii), explicit in the `apply` instruction:**

- **Tight loop** — implementation defect / failing test / review nit: run
  `systematic-debugging` → fix → re-verify, staying in `apply`. No "done" claim
  without green evidence (`verification-before-completion`).
- **Wide loop** — the work reveals a requirement is wrong/missing/ambiguous
  (spec ↔ implementation drift): **stop, surface, amend `requirements`/`specs`
  = open a follow-up change** (OpenSpec living-specs). Never silently patch
  around a spec gap.
- **Conformance check** ("does the code match the requirements?"): the
  requirements↔code traceability table — every acceptance criterion must have
  passing evidence; drift triggers the wide loop. Modeled on spec-kit
  `/analyze`.

## 7. Reference projects — core strengths and where they fold in

All three primary references are **MIT-licensed**.

- **superpowers** (MIT, © 2025 Jesse Vincent): design-before-code hard gate;
  bite-sized TDD task steps (red/green/commit); subagent per-task review gate;
  `systematic-debugging`; `verification-before-completion` (evidence before
  claims); requesting/receiving-code-review. → folds into `tasks`, `apply`
  loop, and the two-radius feedback model.
- **gstack** (MIT, © 2026 Garry Tan): role-based quality gates (eng-review locks
  architecture, `cso` runs OWASP+STRIDE, `qa` uses a real browser, review finds
  production bugs, land-and-deploy ships); `browse` headless-Chromium browser
  capability. → folds into `acceptance-and-review`, `security-baseline`,
  `launch-readiness`, and the (tool-agnostic) browser-verification reference.
- **agent-skills** (MIT, © 2025 Addy Osmani): the full lifecycle map
  DEFINE→PLAN→BUILD→VERIFY→REVIEW→SHIP (our north star); high-quality
  per-concern skills + checklists (api-design, observability, security, TDD,
  ci-cd, documentation-and-adrs); "reframe requirements as success criteria";
  six-core-areas spec template. → primary content source for nearly every
  artifact instruction.

**Methodology references (content sources, not code):** GitHub spec-kit
(constitution; `/analyze` consistency gate), AWS Kiro (EARS notation; dependency
"waves"), BMAD-METHOD (greenfield analysis→planning→solutioning→implementation),
arc42 + C4 (architecture), AWS/Azure Well-Architected (the -ilities), The
Twelve-Factor App (config/scaffold), EARS, ADR/MADR.

## 8. Browser verification

Browser-based verification is referenced **tool-agnostically** inside the
relevant verify steps (Playwright / chrome-devtools-mcp / gstack `browse` as
options). The appendix/README **recommends gstack `browse`** as a concrete
option. Verify instructions prompt the user to **install and run browser tests
after explicit consent** — nothing is vendored into this repo. `agent-skills`'
`browser-testing-with-devtools` is the content blueprint.

## 9. Licensing & attribution

All references are MIT, which permits verbatim copying **provided the original
copyright + permission notice is preserved**. Therefore:

1. A repo-level `CREDITS.md` (and/or `NOTICE`) lists each source's MIT copyright.
2. Verbatim-copied sections carry a near-by attribution
   (e.g. `Adapted from agent-skills (MIT, © 2025 Addy Osmani)`).
3. Prefer adaptation/synthesis for a single coherent voice and clean licensing;
   copy verbatim only where it is clearly best.

A bare README acknowledgement is **not** sufficient for verbatim MIT copying —
the copyright + permission notice must travel with the copied text.

## 10. Non-goals (this design)

- Other schemas (feature, bugfix, refactor, release, product-discovery, …) — the
  library grows after the flagship; each gets its own spec.
- Cross-schema lifecycle chaining / OpenSpec `initiative` coordination.
- Publishing as an installable package (later; v1 = copy / user-override).
- Repeated production deploys/releases — that is a future `release` schema;
  `greenfield-bootstrap` stops at `launch-readiness`.

## 11. Information gathering & progressive interviewing

How does a 13-artifact schema collect information and question the user? **Not
via `explore`** — that is an optional, open-ended upstream "thinking stance"
that deliberately has no fixed steps and does not cover the artifact set.
Questioning happens **per artifact during creation** (`opsx:continue`/`ff`),
driven by each artifact's `instruction` plus the command's "ask the user if
context is unclear" guardrail; each artifact reads its completed dependencies
first, so it only asks about what is genuinely new.

To keep this coherent rather than 13 disjoint interrogations, every artifact
instruction carries a **Clarifying before drafting** protocol:

- **Material-unknown gate**: before writing the file, the agent lists the
  unknowns that would change this artifact or a downstream decision, resolves
  what it can from dependencies and the codebase, and **asks the user about the
  rest** — *only* material ones; trivial unknowns get a sensible default.
- **Brainstorming-style asking** (per `superpowers:brainstorming`): one question
  at a time, multiple-choice when possible, each with the agent's recommended
  default.
- **No parking**: an `Open Questions`/deferred entry is only for items the user
  explicitly chose to defer — never a substitute for asking. (This is the fix
  for the failure mode where `opsx:continue`, being a draft-and-stop operation,
  let the agent dump questions into the doc instead of asking.)
- **DEFINE/primary artifacts** (`product-brief`, `constitution`, `specs`;
  feature's `proposal`/`specs`; etc.) gather broadly; **downstream artifacts**
  lean on dependencies and ask only about newly-introduced decisions.

A schema only supplies instructions, so this strongly biases the agent to ask
but cannot add a hard CLI gate. Cadence is the user's choice: `opsx:continue`
(per artifact) vs `opsx:ff` (draft all, fewer questions); `opsx:explore` is the
fully-interactive upstream option. Future schemas reuse this protocol.

## 12. Open questions

- Exact `requires` DAG and which PLAN artifacts `tasks` formally depends on
  (finalized in `schema.yaml`).
- Whether `modules/` instruction blocks are physically separate files or just an
  authoring convention (no OpenSpec mechanism enforces either).
- Naming of the constitution-injects-config step (artifact instruction vs apply
  side-effect).
