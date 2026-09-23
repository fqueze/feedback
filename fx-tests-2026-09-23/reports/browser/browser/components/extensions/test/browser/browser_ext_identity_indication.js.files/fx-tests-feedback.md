## Question: where does the cascade in each failing job start? (the test that fails first in the same browser)

- Command: `fx-tests task <id> --profiles --limit 0`, for each of the 41 failing tasks of `fx-tests test <path> --task-ids`.
- Expected: for a test that fails in a cascade (40+ tests in the same manifest failing the same harness check), each job's first failing test in run order, and a hint that the failure is a cascade.
- Got: the FAILED list is not in run order. It came out roughly alphabetical, with tests that also failed their retry first, so the leading row was `browser_ext_incognito_views.js` in one job. Run order needed the resource-usage profile. Nothing across jobs said the same test started the cascade in all 41.
- Workaround: saved `fx-tests task <id> --messages --limit 0 --json` for all 41 tasks, then a Python script sorted the manifest's failures by path and printed the first one with the vsync message and its messages.
- What would have answered it: `fx-tests test <path>` flagging "fails after X in the same browser in n/m jobs", or `fx-tests task` listing failures in execution order.

## `fx-tests test <path> --bugs` printed no bug section at all

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_identity_indication.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no line about bugs, so "none found" cannot be told apart from "flag ignored".
