# Tasks: [Feature Name]

## 1. [Phase]
- [ ] 1.1 Write the failing test for [one unit of behavior]
- [ ] 1.2 Run it — confirm it fails for the right reason
- [ ] 1.3 Write the minimal code to pass
- [ ] 1.4 Run the test — confirm it passes
- [ ] 1.5 Commit

[One checkbox per STEP (~2-5 min), NOT one per file. Repeat 1.1–1.5 per unit of
behavior. Every concern delta must appear here as concrete build tasks —
api-contract, data-model, observability (signals), security-baseline (controls),
ux; a delta that has a plan but no build task is a gap.]

## N. Verification
- [ ] N.1 Run affected/full test suite — expect pass
- [ ] N.2 Lint / typecheck / build — expect pass
- [ ] N.3 Run the app the way a user does; open the feature's entry URL/flow and confirm it works end-to-end — NOT 404/blank. Capture the status/screenshot.
- [ ] N.4 Observability & security smoke

## N+1. Review
- [ ] N+1.1 [Code review against the acceptance-and-review checklist]
- [ ] N+1.2 [Requirements ↔ code traceability check]
