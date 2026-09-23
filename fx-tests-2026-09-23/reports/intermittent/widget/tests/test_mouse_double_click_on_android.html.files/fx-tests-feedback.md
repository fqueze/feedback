## Question: did the harness re-run tests in this job, and around which test

- Command: `fx-tests task <taskId> --profiles` (and `fx-tests test <path> --task-ids`)
- Expected: a note when tests ran more than once in a job without failing, since on Android xorig the harness page reloads and re-runs a whole manifest (here: widget/tests re-ran its first 9 tests 1-2 times, right before the failing test).
- Got: `fx-tests task` reports "49 tests, 67 executions" but not which tests ran twice; the failing test's row says "1 failing execution of 1". Only `--passed --limit 0 --json` plus a script over `executionCount` showed it.
- Workaround: `fx-tests task <id> --passed --limit 0 --json | python3 ...` counting `executionCount > 1` per directory.
- Could have shown: "Re-executed without failure: widget/tests (9 tests x2)" in the default output, with the test that ran just before the reruns.

## `fx-tests test` Issues says "Failure details not recorded" for all 46 failures of an Android test

- Command: `fx-tests test widget/tests/test_mouse_double_click_on_android.html`
- Expected: the failure message (`Test timed out.` / `[SimpleTest.finish()] No checks actually run.`), which `fx-tests task <id>` and the resource-usage profile both have.
- Got: `46x FAIL Failure details not recorded (likely Android or platform logging issue)`.
- Workaround: `fx-tests task` on individual jobs, or `fx-tests intermittent --bug <N>`.
