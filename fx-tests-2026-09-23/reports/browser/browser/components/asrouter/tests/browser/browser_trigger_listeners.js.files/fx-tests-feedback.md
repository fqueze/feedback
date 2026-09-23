# fx-tests feedback

## A TEST-KNOWN-FAIL is reported as the run's first failure

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_trigger_listeners.js` (also `fx-tests task <id> --profiles`)
- Expected: Issues (first failure per run) to name the first TEST-UNEXPECTED-FAIL: `Uncaught exception in test - TypeError: Attempted to wrap sendTriggerMessage which is already wrapped`.
- Got: `14x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: button`. In the profile that message is a `TEST-KNOWN-FAIL` (a11y-checks todo, logged in passing runs too); `fx-tests task --profiles` also shows it as the headline for this test and hides the real one among "+12 more messages".
- Cost: this made the 14 runs look like an a11y-check failure; only the profile's Test markers showed the real failure. It also hid that all 14 are the same leak cascade as the other asrouter tests in the job.
- Suggestion: exclude expected-fail statuses (KNOWN-FAIL / todo) when choosing a run's first failure.

## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_feature_callout_in_chrome.js --bugs`
- Expected: a "Bugs: none" line.
- Got: the normal summary with no bugs section, so "no bug" and "flag ignored" look the same.
