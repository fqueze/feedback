## GeckoView junit tests are invisible to `fx-tests test`
- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/TextInputDelegateTest.kt`
- Expected: rates per config, the failure modes, and `--history`, as for a mochitest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". No run counts, so no failure rate.
- Workaround: `fx-tests intermittent --bug 2033328 --since 30 --history` for sheriff-starred counts. Then a script over the Treeherder REST API (`/api/jobs/?push_id=N&result=testfailed` plus `text_log_errors`) over about 500 pushes, to find unstarred failures since a given date. That took about 20 minutes of wall time, including one 10-minute timeout.
- Question it could have answered: "has this junit test failed on trunk since date X, and how many runs had it?"

## `fx-tests task` on a geckoview-junit job reports "0 tests"
- Command: `fx-tests task UUavVdMYRluCWL7V4AgmCQ --profiles`
- Expected: the per-test table. The job's resource-usage profile has 1457 `test` markers with statuses.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all". It also warned that "128 failing markers ... named no test path", and those are `assumeThat` skips that the harness logs as `TEST-FAIL`, not failures.
- Workaround: loaded the resource-usage profile in profiler-cli and searched the `test` markers by name.

## One junit test's ErrorCollector errors: only the last one reaches the failure line
- Not strictly fx-tests, but it shows in `fx-tests intermittent --bug`: the "Failure messages" section gives the TEST-UNEXPECTED-FAIL message, which here is the last of 6 errors collected in one run. The first one, which is the real failure (`Can set selection to range Expected <(4, 4)> but was <(3, 3)>`), appears only in the end-of-run "There were N failures:" summary at the end of the log.
- Question it could have answered: "all assertion errors of this one failing test run".
