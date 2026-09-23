## `fx-tests test` names a TEST-KNOWN-FAIL as the failure mode (again, for browser_siteData3.js)

- Command: `fx-tests test browser/components/preferences/tests/siteData/browser_siteData3.js`
- Expected: Issues to list `This test exceeded the timeout threshold ... limit was 45s` for the 48 linux a11y-checks runs.
- Got: `1. 48x FAIL changed preference: identity.fxaccounts.account.device.name`. In the profiles that message is `TEST-KNOWN-FAIL`; the TEST-UNEXPECTED-FAIL is the duration check logged right after it. Same issue as logged for browser_siteData2.js.
- Workaround: `fx-tests task <id> --messages` for each of the 50 tasks (a shell loop), then the per-test profile's Test markers.

## Question: "which failure message is the unexpected one, across all failing tasks of this test"

- Command: a loop of `fx-tests task <id> --messages` over the 50 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping the output.
- What the output could have shown: `fx-tests test <path>` could count, per issue, the runs whose message was the TEST-UNEXPECTED-* one, or `--task-ids` could print each task's messages inline.
- Also: `--task-ids` prints `e9B-kchgTqCvEnhq3YT97g.1` (run 1), but passing that ID without `.1` to `fx-tests task` silently reads run 0, which had no failure for this test; the loop's output for it was empty.
