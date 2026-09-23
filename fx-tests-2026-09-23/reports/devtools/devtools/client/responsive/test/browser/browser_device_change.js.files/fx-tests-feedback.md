## `test` reports an expected failure (`TEST-FAIL`, todo) as the run's failure

- Command: `fx-tests test devtools/client/responsive/test/browser/browser_device_change.js` (also with `--task-ids --full-messages`)
- Expected: under "Issues (first failure per run)", the run's unexpected failure, `Got an error: AbortError: Actor 'SpecialPowers' destroyed before query 'Spawn' was resolved`.
- Got: `handleEvent() was unable to perform a11y checks on hidden node: ... device-selector-item iconic`. In the log that line is `INFO - TEST-FAIL` (an expected failure; the profile marks it `TEST-KNOWN-FAIL`), and it is logged on every device selection, in passing runs too. `fx-tests task <id> --messages` lists all three messages at "1x" each, with no way to tell which one was unexpected. The third, `Assertion count 2 is greater than expected range`, is always todo in browser-test.js as well.
- Workaround: read the `TEST-UNEXPECTED-FAIL` marker in the per-test profile.
- Would have helped: rank `TEST-UNEXPECTED-*` above `TEST-FAIL`/`TEST-KNOWN-FAIL` when choosing the "first failure", or label each message's status in `task --messages`.
