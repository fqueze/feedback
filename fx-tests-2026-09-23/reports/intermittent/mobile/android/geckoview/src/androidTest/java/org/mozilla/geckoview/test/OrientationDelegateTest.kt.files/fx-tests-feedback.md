## GeckoView junit tests are invisible to `fx-tests test`

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/OrientationDelegateTest.kt`
- Expected: run and failure counts per config (geckoview-junit-fis / -nofis), like for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". No rate, no per-config table, so "fails only on nofis, never on fis" had to be established by hand from the task group and `intermittent --bug`.
- Workaround: `fx-tests intermittent --bug 1768489 --since 120` for annotations, then listing the push's task group through the Taskcluster API and grepping each junit job's `live_backing.log` for `TEST-PASS|TEST-UNEXPECTED-FAIL`.

## `fx-tests task` says a junit job's resource profile records no tests, but it holds 1457 `test` markers

- Command: `fx-tests task IHFqZ4KMRei1rPNY8yENNg --profiles`
- Expected: the job's test outcomes, with this test's FAIL.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". Plus a warning that "129 failing markers in this job named no test path", although the job log says `Failed: 1` (the other 128 look like the junit `Todo` entries).
- Loading the same `profile_resource-usage.json` in profiler-cli shows 1457 `test` markers named `org.mozilla.geckoview.test.Class#method`, with `Status: FAIL` only on this test. The markers are there; they are just not keyed by a file path.
- Workaround: profiler-cli `thread markers --search OrientationDelegateTest --list`.

## The question behind the Taskcluster/JSON scripting: "which configs does this bug's failure happen on, versus where the test runs and passes"

- Command: `fx-tests intermittent --bug 1768489 --since 120 --json` piped to a script, to find the one `geckoview-junit-fis` annotation and see it was a misstar (its line names `InteractiveWidgetTest#bug1994311`).
- The default output lists "Job names" and "Failure messages" as separate histograms, so it cannot say that 83 of 83 onOrientationUnlock messages are nofis and the only fis annotation carries another test's message. A cross-tab of message by job name would have answered it.
