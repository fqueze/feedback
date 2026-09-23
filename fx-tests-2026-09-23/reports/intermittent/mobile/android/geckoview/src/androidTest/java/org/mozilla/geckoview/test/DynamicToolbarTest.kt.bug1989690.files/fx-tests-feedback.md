## geckoview-junit tests have no data in `fx-tests test`

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/DynamicToolbarTest.kt`
- Expected: per-config pass/fail rates for the junit test, as for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`, so nothing on rates, configs or history.
- Workaround: Treeherder `api/failuresbybug` for the annotated failures and `api/project/<repo>/jobs/?job_type_name=...` for job counts per config. That took about 10 calls, and the counts are per job rather than per test run.
- Question it could have answered: "on which configs does this junit test fail, and how often, relative to how often it runs there?" The answer here is that it fails only on opt nofis and never on fis or debug.

## `fx-tests task <junit task> --profiles` reports "0 tests"

- Command: `fx-tests task Eq5efXC0SiuLocTLi4nWIg --profiles`
- Expected: the failing junit test and its neighbours, from the resource-usage profile's `test` markers. Those markers exist (for example `FAIL — org.mozilla.geckoview.test.DynamicToolbarTest#hitTestOnPositionStickyOnMainThread`).
- Got: `0 tests, 0 executions, 0 failing`, plus a warning that 125 failing markers named no test path.
- Workaround: load the profile in profiler-cli and filter the `test` markers by name.
