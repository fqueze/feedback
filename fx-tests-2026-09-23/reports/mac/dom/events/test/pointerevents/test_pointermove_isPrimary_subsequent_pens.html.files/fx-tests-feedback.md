## "Failure details not recorded" for every failure, though the job has them

- Command: `fx-tests test dom/events/test/pointerevents/test_pointermove_isPrimary_subsequent_pens.html`
- Expected: the failure modes under Issues.
- Got: `1. 112x FAIL Failure details not recorded (likely Android or platform logging issue)`, on macOS jobs. `fx-tests task <taskId> --profiles` on any of those jobs prints the message (`[SimpleTest.finish()] No checks actually run. ...`). Same for `test_synthesized_touch.html` (341x).
- Workaround: ran `fx-tests task` on several jobs one by one.

## Which message is the first failure

- Command: `fx-tests task PuYqp4ryT5alfuUbcirNKA --profiles`
- Got: `[SimpleTest.finish()] No checks actually run.` as the test's failure. In the profile, the first TEST-UNEXPECTED-FAIL is `Test timed out.` (in the Subtest field, with an empty Message), one second earlier. The printed message hides that this is a 5-minute timeout.
- Expected: the first TEST-UNEXPECTED-FAIL, or at least show the Subtest when the Message is empty.

## Question: what fraction of jobs fail?

- Command: `fx-tests test <path>`
- Got: `test-macosx1500-aarch64/debug-mochitest-plain 24.5% 106 432`. The 432 runs appear to include the harness retries: 432-106 = 326, and the two tests that fail in the same jobs give 624-298 = 326 and 636-310 = 326. So per job it is 106/326 = 32.5%, and 91-95% for the co-failing tests, which read as 48% in the table.
- What would have answered it: a jobs column next to runs, or saying in the table whether retries are counted.
