# Harden `tasks` & `project-scaffold` instructions — design

> Date: 2026-06-21 · Schema: `greenfield-bootstrap` · Scope: instruction text only

## Problem (from a real dogfood run)

A `cost-analytics` MVP was run end-to-end through `greenfield-bootstrap` (latest
schema) on a separate machine and archived. Reviewing the generated artifacts
surfaced three failures that all trace to the **same root cause**:

1. **`tasks.md` granularity was wrong** — one checkbox per *file* ("create
   `database.py` (engine, SessionLocal, Base, get_db)"), tests batched into a
   single trailing phase (test-after, frontend untested), and **no `##
   Verification` / `## Review` phases at all**.
2. **The app "wouldn't open" after apply** — `project-scaffold.md` had no `## Run
   & Entry` section; it shipped two separate servers (FastAPI `:8000` API-only +
   Vite `:5173`) with the backend root 404ing and nothing naming the entry URL.
3. **Durable knowledge didn't persist** — no `docs/` tree was written; the
   architecture/observability/security knowledge survives only inside the archive
   folder, not as living repo docs.

## Root cause

The schema *templates* are already correct — `templates/tasks.md` pre-seeds the
TDD step skeleton, `## Verification` (with the entry-URL gate), and `## Review`;
`templates/project-scaffold.md` has a `## Run & Entry` section. **The generating
model discarded the template structure** — it replaced those headings with its
own ("Bootstrap Commands", a trailing "tests"/"收尾" phase). The instructions
described the right outcome but as long prose the model felt free to ignore.

So the fix is **enforcement, not new content**: make the non-negotiables short,
salient, and front-loaded so they survive even when the template is treated as a
soft suggestion.

## Changes (all in `openspec/schemas/greenfield-bootstrap/schema.yaml`)

### A. `tasks` instruction
- Prepend a numbered **HARD RULES** block (rules first, rationale after):
  1. Keep template structure: build phases → `## Verification` → `## Review`,
     verbatim; never rename/merge/drop/replace them.
  2. One checkbox = one STEP (~2-5 min), not one file — with a ✅/❌ example pair.
  3. Tests interleave with code (TDD), never batched; frontend included.
  4. `## Verification` must include a checkbox that runs the app, opens the entry
     URL, and confirms it serves (not 404/blank).
  5. Every PLAN artifact → ≥1 concrete task, including a `docs/` durability task.
- Collapse the now-redundant prose "Structure" bullets into the rules.
- Append a **落盘前自检 (self-check)** paragraph the model must pass before saving.

### B. `project-scaffold` instruction
- Prepend a **HARD RULES** block: keep template headings (esp. `## Run & Entry`),
  don't replace template sections; `## Run & Entry` must give one run command +
  one named entry URL + how FE/BE connect, with the backend root not 404ing.
- Append a self-check line.

### C. `apply` instruction (defensive, one line)
- If `tasks.md` is missing `## Verification`/`## Review`, treat it as a generation
  defect and add the phase (incl. the run-app/entry-URL check) before continuing.

## Non-goals
- No changes to the other 12 artifacts (none failed this run — YAGNI).
- No new lint/validation tooling (OpenSpec wouldn't auto-invoke it; deferred).
- Templates stay as-is (they were already correct).

## Validation
`openspec schema validate greenfield-bootstrap` must pass green before commit.
