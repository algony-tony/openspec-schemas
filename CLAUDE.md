# CLAUDE.md — openspec-schemas

## What this project is

`openspec-schemas` is a **reusable, MIT-licensed library of custom OpenSpec
workflow schemas** covering full-stack software development plus auxiliary
workflows. Schemas are technology-stack agnostic so they can be published and
consumed by any project.

The flagship schema is **`greenfield-bootstrap`** — standing up a new full-stack
project from a fuzzy idea to a verified, launch-ready MVP. Full design:
[`docs/superpowers/specs/2026-06-19-openspec-schemas-greenfield-design.md`](docs/superpowers/specs/2026-06-19-openspec-schemas-greenfield-design.md).

## Repository layout

- `openspec/schemas/<name>/` — authored schemas (`schema.yaml` + `templates/*.md`);
  this is where OpenSpec resolves project-local schemas, so the repo dogfoods them
- `docs/superpowers/{specs,plans}/` — design docs & implementation plans
- `CREDITS.md` / `NOTICE` — third-party attribution (all references are MIT)

> A shared `modules/` directory for reusable instruction blocks is deferred until
> a second schema needs reuse (YAGNI).

## How OpenSpec schemas work (quick reference)

- An artifact has: `id`, `generates`, `description`, `template`, `instruction?`,
  `requires[]`. **No `optional` flag, no `when`/`condition`.** "Skip" = write a
  thin "N/A, because…" artifact.
- A schema has: `name`, `version`, `description?`, `artifacts[]`, `apply?`.
  **No `extends`/`include`** — share instruction content by maintaining canonical
  modules in the repo and composing (copy/adapt) them into each schema.
- `openspec/config.yaml` `context` is injected into **every** artifact
  instruction; `rules` are injected per-artifact.
- Useful commands: `openspec schemas`, `openspec schema validate <name>`,
  `openspec schema init <name>`, `openspec schema fork <src> <name>`,
  `openspec templates --schema <name>`.

## Git principles

Follow these for every change in this repo:

1. **Commit early and often.** Small, focused, logically-scoped commits — one
   concern per commit. A commit should leave the repo in a coherent state.
2. **Conventional Commits.** Prefix messages: `feat:`, `fix:`, `docs:`,
   `refactor:`, `chore:`, `test:`. Subject in the imperative, ≤72 chars.
3. **`main` is the integration branch.** Do feature work on a branch
   (`feat/…`, `design/…`, `fix/…`); merge to `main` via PR. Do not commit
   work-in-progress directly to `main`.
4. **Keep the remote in sync.** Push after each meaningful commit so work is
   backed up and visible. `origin` is the GitHub repo of the same name.
5. **Never commit secrets** or generated artifacts; respect `.gitignore`.
6. **Verify before committing.** Where applicable run
   `openspec schema validate` (and any tests) and confirm green before commit.
7. **AI-assisted commits** end the message with:
   `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`.

## Working agreements

- **Design before code.** Brainstorm → design doc (in `docs/superpowers/specs/`)
  → implementation plan → build. Don't jump straight to authoring a schema.
- **Tool-agnostic instructions.** Reference capabilities, not specific tools;
  concrete tool recommendations go in appendices/README.
- **Attribution travels with copied text.** MIT permits verbatim copying only
  with the original copyright + permission notice preserved (see `CREDITS.md`).
