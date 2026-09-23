## `fx-tests test` has no geckoview-junit data

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/DynamicToolbarTest.kt` (also `fx-tests test DynamicToolbarTest`)
- Expected: rates per config, history, and task ids for a geckoview-junit test (the ids are `org.mozilla.geckoview.test.DynamicToolbarTest#method`, not paths).
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`, with no hint that junit is simply not indexed.
- Workaround: `fx-tests intermittent --bug 2033703 --limit 0` for task ids, Treeherder `api/failurecount/?bug=` for a per-push rate, then job logs.
- Question it could have answered: "how often does this junit test fail, and on which configs, relative to its runs".

## `fx-tests task` on a geckoview-junit job: "0 tests" and assumption skips counted as failing

- Command: `fx-tests task fwuslfD7RayyFHZb5FCgYg --profiles`
- Expected: the job's tests with their status, the one real failure being `DynamicToolbarTest#showDynamicToolbarOnFrameReconstruction`.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all", plus a warning that "133 failing markers ... named no test path". Those 133 include `DynamicToolbarTest#showDynamicToolbar` and `#noGapAppearsBetweenBodyAndElementFullyCoveringBody`. These are `AssumptionViolatedException` skips, which the harness logs as `TEST-FAIL` (expected, INSTRUMENTATION_STATUS_CODE -4), not as `TEST-UNEXPECTED-FAIL`.
- Workaround: `profiler-cli thread markers --search <name> --list` on the resource-usage profile, and grepping the log.
