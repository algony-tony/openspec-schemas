# Fix Plan: [short title]

## Fix
[The change that removes the root cause.]

## Regression Test
[The failing test to write first that reproduces the bug.]

## Blast Radius
[What else this change can affect.]

## Spec Impact
[Does the bug reveal a spec/requirements gap? If yes, this is a spec change
(wide loop), not just a code patch — note the follow-up.]

## Prevention
[Beyond the one regression test: what systemic change stops this CLASS of bug
from recurring? e.g. a lint rule, a type/invariant, a contract test, a removed
footgun, or a doc/runbook note. Or "none warranted, because …".]
