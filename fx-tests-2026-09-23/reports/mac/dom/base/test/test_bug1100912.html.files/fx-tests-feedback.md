## "Which manifest ran just before mine, in each failing job?" needed the raw logs

- Command: `fx-tests task <taskId> --profiles` for each of the 9 failing jobs of `dom/base/test/test_bug1100912.html`.
- Question: the test fails in a fresh browser (run-by-manifest), so the leaker suspect is the previous manifest's browser. `task` lists what failed, not the manifest order.
- Workaround: downloaded each `live_backing.log` and grepped `Running manifest:`; the answer was `dom/base/test/fullscreen/mochitest.toml` in all 9.
- What its output could have shown: for a failing test, its manifest and the manifest that ran immediately before it in that job (and whether that one passed), e.g. a `Previous manifest:` line under each failure.

## The failure message shown is the fallout, not the failure

- Command: `fx-tests task Qhag1V4PSkmuob-XcJocRw --profiles` and `fx-tests test dom/base/test/test_bug1100912.html` (Issues: `21x FAIL Failure details not recorded`).
- Expected: `Test timed out.` — the first TEST-UNEXPECTED-FAIL (from `TestRunner._checkForHangs`).
- Got: `task` shows `[SimpleTest.finish()] No checks actually run.`, which is logged by `killTest` one second after the timeout; `test` shows no message at all.
- Workaround: read the TestStatus markers in the per-test profile (`subtest` field `Test timed out.`).

