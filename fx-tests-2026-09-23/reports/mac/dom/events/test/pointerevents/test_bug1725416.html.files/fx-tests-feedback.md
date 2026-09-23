## `fx-tests test` Issues list says "Failure details not recorded" for a failure whose message is known

- Command: `fx-tests test dom/events/test/pointerevents/test_bug1725416.html`
- Expected: under Issues, the failure message of the 13 failing runs (`Test timed out.` first, then `[SimpleTest.finish()] No checks actually run. ...`), and the runs counted as timeouts.
- Got: `13x FAIL Failure details not recorded (likely Android or platform logging issue)`, and `0 timeout` in the summary line. `fx-tests task <id>` for each of the 13 jobs does print a message (`[SimpleTest.finish()] No checks actually run.`), so the data exists.
- Workaround: ran `fx-tests task` on all 13 task IDs to get the message.

## `fx-tests task` shows the second failure message of the run, not the first

- Command: `fx-tests task SVm3BreeSDCBA8cmnOI76A --profiles`
- Expected: the first failure, `Test timed out.` (a TestStatus with subtest `Test timed out.` and an empty message, logged by `TestRunner._checkForHangs`), and the run classed TIMEOUT.
- Got: `FAIL` with `[SimpleTest.finish()] No checks actually run.`, which is the fallout of the timeout. Read alone it suggests the test finished without running anything.
- Workaround: the resource-usage profile and the per-test profile both show the `Test timed out.` marker one second earlier.
