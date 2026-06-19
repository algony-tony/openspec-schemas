# Tasks: [Project Name]

## 1. [Phase — e.g. Data model]
- [ ] 1.1 Write the failing test for [one unit of behavior]
- [ ] 1.2 Run it — confirm it fails for the right reason
- [ ] 1.3 Write the minimal code to pass
- [ ] 1.4 Run the test — confirm it passes
- [ ] 1.5 Commit

[One checkbox per STEP (~2-5 min), NOT one per file. Repeat 1.1–1.5 per unit of
behavior. Every PLAN artifact must appear here as concrete build tasks —
api-contract, data-model, quality-strategy (each test level), observability
(each signal/health/SLO), security-baseline (each control/abuse-case test), ux;
a concern that has a plan but no build task is a gap.]

## N. Verification
- [ ] N.1 Run full test suite — expect pass
- [ ] N.2 Lint / typecheck / build — expect pass
- [ ] N.3 Run the app the way a user does (project-scaffold's run command); open the entry URL and confirm it serves a working page — NOT 404/blank. Capture the status/screenshot.
- [ ] N.4 Exercise one core user flow end-to-end through that entry (not just the API)
- [ ] N.5 Observability & security smoke

## N+1. Review
- [ ] N+1.1 [Code review against the acceptance-and-review checklist]
- [ ] N+1.2 [Requirements ↔ code traceability check]
