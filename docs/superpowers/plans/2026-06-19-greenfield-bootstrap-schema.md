# greenfield-bootstrap Schema Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Author the `greenfield-bootstrap` OpenSpec schema — a 13-artifact
workflow (DEFINE→PLAN→BUILD→VERIFY→REVIEW→SHIP) plus an `apply` feedback loop —
as a valid, project-local schema in this repo.

**Architecture:** A schema is `schema.yaml` (artifact chain with
`instruction`/`requires`/`apply`) + `templates/*.md`. We scaffold with
`openspec schema init`, then author each artifact's template + instruction +
`requires`, validating after each. Instruction prose is drawn from the
MIT-licensed reference repos cloned at `/home/zhu/repos/{agent-skills,superpowers,gstack}`.

**Tech Stack:** OpenSpec CLI v1.4.1 (`@fission-ai/openspec`), YAML, Markdown.

**Spec:** `docs/superpowers/specs/2026-06-19-openspec-schemas-greenfield-design.md`

---

## Testing model (this is content authoring, not app code)

There is no unit-test harness. Each task's gate is:
1. **Structural:** `openspec schema validate greenfield-bootstrap` → reports valid.
2. **Content checklist:** the artifact's `instruction` covers every bullet listed
   in its task, and the template contains the listed headers.

A final **smoke task** creates a throwaway change with the schema and walks the
whole artifact chain via `openspec status` / `openspec instructions`, then
deletes it.

## File structure

```
openspec/schemas/greenfield-bootstrap/
  schema.yaml
  templates/
    product-brief.md  constitution.md  requirements.md  architecture.md
    api-contract.md   data-model.md    quality-strategy.md  observability.md
    security-baseline.md  project-scaffold.md  acceptance-and-review.md
    tasks.md  launch-readiness.md
```

All artifacts use `generates: <id>.md` and `template: <id>.md`, except `tasks`
which uses `generates: tasks.md`. The final `requires` DAG:

| id | requires |
|---|---|
| product-brief | — |
| constitution | product-brief |
| requirements | product-brief, constitution |
| architecture | requirements, constitution |
| api-contract / data-model / quality-strategy / observability / security-baseline / project-scaffold | architecture |
| acceptance-and-review | requirements, quality-strategy |
| tasks | architecture, api-contract, data-model, quality-strategy, acceptance-and-review, project-scaffold |
| launch-readiness | observability, security-baseline, project-scaffold, acceptance-and-review |

`apply`: `requires: [tasks, launch-readiness]`, `tracks: tasks.md`.

## Authoring procedure (applies to every artifact task, Tasks 2–14)

For each artifact:
1. **Write the template** `templates/<id>.md` with the headers shown in the task.
2. **Write the `instruction`** for that artifact in `schema.yaml`, covering every
   bullet in the task, grounded in the named source file.
3. **Set** `id`, `generates`, `description`, `template`, and `requires` per the
   DAG table.
4. **Validate:** `openspec schema validate greenfield-bootstrap` → expect
   `valid` (no errors).
5. **Content check:** confirm the template headers and instruction bullets are
   all present.
6. **Commit:** `git commit -m "feat(greenfield): add <id> artifact"` (end with
   the Co-Authored-By trailer).

---

## Task 1: Scaffold the schema (green baseline)

**Files:**
- Create: `openspec/schemas/greenfield-bootstrap/schema.yaml`
- Create: `openspec/schemas/greenfield-bootstrap/templates/*.md`

> **Mechanism note (corrected during execution):** `openspec schema init
> --artifacts` only accepts the built-in ids `proposal,specs,design,tasks` — it
> rejects custom artifact names. So we **fork** the package schema to get the
> correct project-local location, then hand-author `schema.yaml`. Because the
> artifact `instruction` field is **optional**, this task lands a valid
> structural skeleton (ids/generates/description/template/requires + `apply`,
> **no instructions**); instructions are added per segment in Tasks 2–15 — no
> placeholder prose is committed.

- [ ] **Step 1: Fork into the project-local location**

```bash
openspec schema fork spec-driven greenfield-bootstrap
```

- [ ] **Step 2: Replace templates and write the skeleton schema.yaml**

Remove the inherited `proposal.md`/`spec.md`/`design.md` templates, write the 13
templates from Tasks 2–14, and overwrite `schema.yaml` with the 13-artifact
skeleton + `apply` block (see the `requires` DAG table above). Leave `instruction`
fields out for now.

- [ ] **Step 3: Verify it is discoverable and valid**

Run: `openspec schemas | grep greenfield-bootstrap && openspec schema validate greenfield-bootstrap`
Expected: schema listed (source: project) and validation reports valid.

- [ ] **Step 4: Commit**

```bash
git add openspec/schemas/greenfield-bootstrap
git commit -m "feat(greenfield): scaffold greenfield-bootstrap schema"
```

---

## Task 2: Artifact `product-brief`

**requires:** `[]`

- [ ] **Template** `templates/product-brief.md`:

```markdown
# Product Brief: [Project Name]

## Vision
[1-2 paragraphs: the problem and the desired outcome.]

## Target Users / Personas
- [Persona] — [needs, context]

## MVP Scope
[What the first shippable version includes.]

## Non-Goals
[Explicitly out of scope for now.]

## Success Metrics
[Measurable signals that the MVP works.]

## Open Questions
[Unknowns needing human input.]
```

- [ ] **Instruction** — source: `agent-skills/skills/idea-refine` + `interview-me`.
  Must instruct the author to:
  - Surface assumptions explicitly before writing (list them, invite correction).
  - Fill each template section; keep MVP scope ruthlessly small (YAGNI).
  - Reframe vague asks as measurable success metrics.
  - Stop and ask the human when scope or users are unclear.

- [ ] Follow the **Authoring procedure** steps 3–6 (requires `[]`).

---

## Task 3: Artifact `constitution`

**requires:** `[product-brief]`

- [ ] **Template** `templates/constitution.md`:

```markdown
# Project Constitution: [Project Name]

## Principles
[Numbered, durable engineering principles for this project.]

## Tech Stack
[Languages, frameworks, key dependencies + versions, with rationale.]

## Boundaries
- **Always:** [...]
- **Ask first:** [...]
- **Never:** [...]

## Config Sync
[Condensed principles + boundaries to copy into openspec/config.yaml context.]
```

- [ ] **Instruction** — source: spec-kit constitution + Agent OS standards +
  `agent-skills/skills/spec-driven-development` (Boundaries three-tier). Must
  instruct the author to:
  - Write 5–10 durable principles and the three-tier Boundaries (Always /
    Ask-first / Never).
  - Record the tech stack with rationale.
  - **Sync to config:** append a condensed version of principles + boundaries
    into `openspec/config.yaml` under `context:` so every future change inherits
    it. Show the exact YAML edit and warn to keep it concise.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 4: Artifact `requirements`

**requires:** `[product-brief, constitution]`

- [ ] **Template** `templates/requirements.md`:

```markdown
# Requirements: [Project Name]

## Functional Requirements

### Requirement: [name]
WHEN [trigger], the system SHALL [response].

#### Acceptance Criteria
- [Testable condition]

## Non-Functional Requirements
[Performance, availability, security targets — link to architecture -ilities.]

## Out of Scope
[...]
```

- [ ] **Instruction** — source: Kiro EARS notation + BMAD PRD +
  `agent-skills/skills/spec-driven-development` ("reframe as success criteria").
  Must instruct the author to:
  - Write functional requirements in EARS form (WHEN/IF/WHILE … SHALL …).
  - Give every requirement at least one **testable** acceptance criterion.
  - Capture non-functional targets and explicit out-of-scope.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 5: Artifact `architecture`

**requires:** `[requirements, constitution]`

- [ ] **Template** `templates/architecture.md`:

```markdown
# Architecture: [Project Name]

## Context (C4 L1)
[Actors, external systems.]

## Containers / Components (C4 L2–L3)
[Major runtime units, responsibilities, boundaries.]

## Key Decisions (ADRs)
### ADR-001: [title]
- **Context:** ...
- **Decision:** ...
- **Consequences:** ...

## Runtime Topology
[Deployment/runtime shape.]

## Quality Attributes (-ilities)
[Scalability, availability, performance, security targets.]
```

- [ ] **Instruction** — source: BMAD solutioning/ADR + arc42 + C4 +
  AWS Well-Architected + `agent-skills/skills/documentation-and-adrs`. Must
  instruct the author to:
  - Describe context and component boundaries (C4 levels 1–3).
  - Record each significant decision as an ADR (Context/Decision/Consequences)
    with alternatives considered.
  - State quality-attribute targets across the Well-Architected pillars.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 6: Artifact `api-contract`

**requires:** `[architecture]`

- [ ] **Template** `templates/api-contract.md`:

```markdown
# API Contract: [Project Name]

## Surfaces
[External + internal interfaces in scope.]

## Resource / Operation Model
[Endpoints/operations with request/response shapes — contract-first.]

## Error Model
[Error categories, codes, shapes.]

## Versioning & Deprecation
[Versioning scheme; deprecation policy.]
```

- [ ] **Instruction** — source: `agent-skills/skills/api-and-interface-design`.
  Must instruct the author to:
  - Define the contract before implementation (contract-first).
  - Apply Hyrum's Law: minimize observable surface; plan deprecation at design.
  - Specify a consistent error model and a versioning/deprecation policy.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 7: Artifact `data-model`

**requires:** `[architecture]`

- [ ] **Template** `templates/data-model.md`:

```markdown
# Data Model: [Project Name]

## Entities (ERD)
[Entities, attributes, relationships.]

## Storage
[Datastore choice + rationale.]

## Migrations & Seed
[Migration strategy, seed data, rollback.]

## Lifecycle & Retention
[Data classification, retention, deletion.]
```

- [ ] **Instruction** — source: design doc §5 (data-model) + Well-Architected
  reliability. Must instruct the author to:
  - Model entities/relationships and choose storage with rationale.
  - Define a forward + rollback migration strategy and seed data.
  - State data classification, retention, and deletion policy.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 8: Artifact `quality-strategy`

**requires:** `[architecture]`

- [ ] **Template** `templates/quality-strategy.md`:

```markdown
# Quality Strategy: [Project Name]

## Test Pyramid
[Unit / integration / e2e / contract — what goes where.]

## TDD (default discipline)
[Red → green → refactor; write the failing test first.]

## Testability Seams
[DI, interfaces, fixtures enabling isolation.]

## Coverage Gates & CI Stages
[Thresholds and CI test stages.]
```

- [ ] **Instruction** — source: `agent-skills/skills/test-driven-development` +
  `superpowers/skills/test-driven-development` + `references/testing-patterns.md`.
  Must instruct the author to:
  - Make TDD the default (failing test first; "if you didn't watch it fail, you
    don't know it tests the right thing"); list exceptions needing human OK.
  - Define the test pyramid, testability seams, coverage gates, and CI stages.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 9: Artifact `observability`

**requires:** `[architecture]`

- [ ] **Template** `templates/observability.md`:

```markdown
# Observability: [Project Name]

## On-call Questions
[2–4 questions on-call will ask about each feature.]

## Signals
[Per question: log / metric / trace, with field/metric names.]

## Health & Readiness
[Health/readiness endpoints and dependencies.]

## SLOs & Alerts
[SLOs, error budgets, alert rules.]
```

- [ ] **Instruction** — source:
  `agent-skills/skills/observability-and-instrumentation`. Must instruct the
  author to:
  - Start from 2–4 questions on-call will ask, then pick a signal per question
    (metric = *that*, trace = *where*, log = *why*).
  - Use structured logging (stable event name + fields), define health/readiness,
    SLOs, and alerts.
  - Treat instrumentation as written alongside the feature, not post-launch.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 10: Artifact `security-baseline`

**requires:** `[architecture]`

- [ ] **Template** `templates/security-baseline.md`:

```markdown
# Security Baseline: [Project Name]

## Trust Boundaries
[Where untrusted data crosses in.]

## Assets
[What is worth protecting.]

## STRIDE Review
[Per boundary: threats + mitigations.]

## Always-Do Controls (OWASP)
[Input validation, parameterized queries, output encoding, HTTPS, password
hashing, security headers, secure cookies, dependency audit.]

## Abuse Cases
[Misuse scenarios → first tests.]

## Data Classification
[PII/secret handling.]
```

- [ ] **Instruction** — source: `agent-skills/skills/security-and-hardening` +
  `references/security-checklist.md` + gstack `cso`. Must instruct the author to:
  - Threat-model first: map trust boundaries, name assets, run STRIDE per
    boundary, write abuse cases next to use cases.
  - List the OWASP "Always Do" controls and "Ask First" changes.
  - Classify data (PII/secrets) and its handling.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 11: Artifact `project-scaffold`

**requires:** `[architecture]`

- [ ] **Template** `templates/project-scaffold.md`:

```markdown
# Project Scaffold: [Project Name]

## Repo Layout
[Directory structure + responsibilities.]

## Tooling
[Lint, format, typecheck, package manager.]

## Config & Environments (12-factor)
[Config strategy, env vars, secrets.]

## CI/CD
[Pipeline stages, gates.]

## IaC & Local Dev
[Infra-as-code baseline, local setup commands.]
```

- [ ] **Instruction** — source: `agent-skills/skills/ci-cd-and-automation` +
  The Twelve-Factor App. Must instruct the author to:
  - Define repo layout, tooling, and 12-factor config/secrets handling.
  - Define CI/CD stages and gates, an IaC baseline, and exact local-dev commands.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 12: Artifact `acceptance-and-review`

**requires:** `[requirements, quality-strategy]`

- [ ] **Template** `templates/acceptance-and-review.md`:

```markdown
# Acceptance & Review: [Project Name]

## Acceptance Criteria (per requirement)
[Requirement → testable acceptance criteria.]

## Definition of Done
[Checklist that marks work complete.]

## Code-Review Checklist
[What reviewers verify.]

## Requirements ↔ Code Traceability
| Requirement | Tests / Evidence |
|---|---|
| [req] | [test ids] |
```

- [ ] **Instruction** — source: spec-kit `/analyze` + gstack reviews +
  `superpowers/skills/requesting-code-review`. Must instruct the author to:
  - Restate each requirement's acceptance criteria as the conformance yardstick.
  - Define the Definition of Done and a concrete code-review checklist.
  - Build the requirements↔code traceability table (every requirement maps to
    evidence); drift here triggers the wide feedback loop in `apply`.

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 13: Artifact `tasks`

**requires:** `[architecture, api-contract, data-model, quality-strategy, acceptance-and-review, project-scaffold]`

- [ ] **Template** `templates/tasks.md`:

```markdown
# Tasks: [Project Name]

## 1. [Phase]
- [ ] 1.1 [Build task — TDD: failing test → run (fail) → minimal impl → run (pass) → commit]

## N. Verification
- [ ] N.1 Run full test suite — expect pass
- [ ] N.2 Lint / typecheck / build — expect pass
- [ ] N.3 Observability & security smoke

## N+1. Review
- [ ] [Code review against the acceptance-and-review checklist]
- [ ] [Requirements ↔ code traceability check]
```

- [ ] **Instruction** — source: `superpowers/skills/writing-plans` (bite-sized
  TDD steps) + Kiro waves. Must instruct the author to:
  - Use `- [ ] X.Y` checkboxes (apply parses these); order by dependency.
  - Expand each build task into TDD steps (failing test → fail → minimal impl →
    pass → commit).
  - End with explicit `## Verification` and `## Review` phases (not just build).

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 14: Artifact `launch-readiness`

**requires:** `[observability, security-baseline, project-scaffold, acceptance-and-review]`

- [ ] **Template** `templates/launch-readiness.md`:

```markdown
# Launch Readiness: [Project Name]

## Pre-Launch Checklist
- [ ] CI green on main
- [ ] Observability wired (dashboards/alerts live)
- [ ] Security baseline controls verified
- [ ] Rollback plan / runbook documented
- [ ] Staging smoke test passed

## Rollback Plan
[How to revert.]

## Runbook
[On-call/operational notes.]
```

- [ ] **Instruction** — source: `agent-skills/skills/shipping-and-launch` +
  gstack `land-and-deploy`. Must instruct the author to:
  - Verify CI, observability, and security controls are live before launch.
  - Require a rollback plan and a runbook, and a staging smoke test.
  - Note: repeated production releases are out of scope (future `release` schema).

- [ ] Follow the **Authoring procedure** steps 3–6.

---

## Task 15: Author the `apply` phase (feedback loop)

**Files:**
- Modify: `openspec/schemas/greenfield-bootstrap/schema.yaml` (`apply:` block)

- [ ] **Step 1: Write the apply block**

Set `apply.requires: [tasks, launch-readiness]`, `apply.tracks: tasks.md`, and an
`instruction` that encodes the two-radius feedback loop from spec §6:
  - Work through `tasks.md`; per build task follow TDD; mark checkboxes as done.
  - Run the `## Verification` and `## Review` phases as gates; no "done" claim
    without green evidence (cite `verification-before-completion`).
  - **Tight loop:** implementation defect / failing test → `systematic-debugging`
    → fix → re-verify, stay in apply.
  - **Wide loop:** requirement is wrong/missing/ambiguous → stop, surface, amend
    `requirements`/`specs` as a follow-up change; never silently patch.
  - **Browser verification (tool-agnostic):** where UI acceptance needs a browser,
    reference Playwright / chrome-devtools-mcp / gstack `browse`; prompt the user
    to install and run **only after explicit consent**; nothing is vendored.

- [ ] **Step 2: Validate**

Run: `openspec schema validate greenfield-bootstrap`
Expected: valid.

- [ ] **Step 3: Commit**

```bash
git add openspec/schemas/greenfield-bootstrap/schema.yaml
git commit -m "feat(greenfield): add apply phase with two-radius feedback loop"
```

---

## Task 16: End-to-end smoke test + finalize

**Files:**
- Temporary: a throwaway change under `openspec/changes/` (deleted at the end)

- [ ] **Step 1: Create a throwaway change with the schema**

```bash
openspec new change smoke-greenfield --schema greenfield-bootstrap
openspec status --change smoke-greenfield --json
```
Expected: status lists all 13 artifacts with the correct dependency order;
`product-brief` is the first artifact with status "ready".

- [ ] **Step 2: Render instructions for a few artifacts**

```bash
openspec instructions product-brief --change smoke-greenfield | head -40
openspec instructions observability --change smoke-greenfield | head -40
openspec instructions tasks --change smoke-greenfield | head -40
```
Expected: each renders the template + instruction + dependency context without error.

- [ ] **Step 3: Tear down the throwaway change**

```bash
rm -rf openspec/changes/smoke-greenfield
```
Expected: `git status` shows no leftover smoke files.

- [ ] **Step 4: Final validation + commit any fixes**

Run: `openspec schema validate greenfield-bootstrap --verbose`
Expected: valid. Fix any issues found, then commit.

- [ ] **Step 5: Update README status**

Mark `greenfield-bootstrap` as "Available" in `README.md` and commit:

```bash
git commit -am "docs: mark greenfield-bootstrap as available"
```

---

## Self-review checklist (run after execution, before opening the PR)

- [ ] All 13 artifacts authored; `openspec schema validate` is clean.
- [ ] Spec §5 artifact table ↔ schema.yaml `requires` match exactly.
- [ ] Spec §6 back-half loop is fully encoded in `apply.instruction`.
- [ ] No template or instruction is a placeholder stub.
- [ ] `constitution` instruction includes the `openspec/config.yaml` sync step.
- [ ] Browser references are tool-agnostic; gstack `browse` only in appendix/README.
