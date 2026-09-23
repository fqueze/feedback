## The failure shown is the fallout, not the failure (same as test_bug1100912.html's feedback)

- Command: `fx-tests test dom/base/test/test_content_iterator_post_order.html` (Issues: `9x FAIL Failure details not recorded`, and `0 timeout`) and `fx-tests task XBnqwgBsQ62h73lbl96uig --profiles`.
- Expected: `Test timed out.` (the first TEST-UNEXPECTED-FAIL, empty message, subtest `Test timed out.`), counted as a timeout.
- Got: `task` shows `[SimpleTest.finish()] No checks actually run.`, logged ~1 s later by the kill; `test` shows no message and counts 0 timeouts.
- Workaround: read the TestStatus markers' `subtest` field in the per-test profile.

## A test killed by "N test timeouts, giving up" disappears from its own counts

- Command: `fx-tests test dom/base/test/test_content_iterator_subtree.html`
- Expected: failures in the jobs where it was the 4th hang (resource-usage profile of XBnqwgBsQ62h73lbl96uig: `dom/base/test/test_content_iterator_subtree.html Test timed out.` at 27m11s, then `(SimpleTest/TestRunner.js) 4 test timeouts, giving up.`).
- Got: `16,781 runs, 0 fail` — 9 runs fewer than its manifest neighbour, i.e. the failing jobs' run is dropped, not counted as a failure. The timeout is filed under `(SimpleTest/TestRunner.js)`.
- Question it could have answered: "which tests hang in this job?" — the last hang before the harness gives up is invisible per test.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test dom/base/test/test_content_iterator_post_order.html --bugs`
- Expected: a line saying no annotated bug names the test.
- Got: the normal output with no bugs section at all, indistinguishable from `--bugs` being ignored.
