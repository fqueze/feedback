## `fx-tests test` counts a timeout as a FAIL with "Failure details not recorded"

- Command: `fx-tests test dom/base/test/test_content_iterator_pre_order.html`
- Expected: 9 timeouts, with `Test timed out.` as the first failure message (it is, in every one of the 9 jobs: a `TEST-UNEXPECTED-FAIL` whose `message` is empty and whose `subtest` is `Test timed out.`, then `[SimpleTest.finish()] No checks actually run.` from the harness's killTest).
- Got: `0 timeout`, and the only Issue is `9x FAIL Failure details not recorded (likely Android or platform logging issue)`. That sends the reader to look for a logging problem, and hides that it is a 5-minute hang.
- Workaround: `fx-tests task <taskId> --profiles`, then the resource-usage profile's `FAIL` markers, to find `Test timed out.`

## `fx-tests task` headlines the fallout, not the timeout

- Command: `fx-tests task Dr9CwGlaSViMZbjcCMwZ7w --profiles`
- Expected: `Test timed out.` as each test's message, since it is the first failure.
- Got: `[SimpleTest.finish()] No checks actually run. ...` for all three tests, which reads as a test that forgot to call `ok()`.
- Also: the fourth test that timed out in the same browser, `dom/base/test/test_content_iterator_subtree.html`, is missing from `FAILED (4)`. Its failures are logged against `(SimpleTest/TestRunner.js)` because the fourth timeout halts the run (`4 test timeouts, giving up.`), so "every test this job recorded a failure for" leaves out the victim that set off the halt.
- Workaround: listing `FAIL` markers in the job's resource-usage profile.

## `--bugs` prints nothing when there is no bug

- Command: `fx-tests test dom/base/test/test_content_iterator_pre_order.html --bugs`
- Expected: a `Bugs` block, or a line saying no annotated bug names this test.
- Got: the same output as without `--bugs`, so I could not tell "no bug" from "the lookup did not run".
