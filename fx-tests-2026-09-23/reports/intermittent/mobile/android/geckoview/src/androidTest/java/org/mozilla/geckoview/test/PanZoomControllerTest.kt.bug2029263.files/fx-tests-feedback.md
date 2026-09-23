# fx-tests feedback (PanZoomControllerTest.kt, bug 2029263)

## GeckoView junit tests are invisible to `fx-tests test`

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/PanZoomControllerTest.kt`
- Expected: per-config pass/fail rates for `PanZoomControllerTest#touchEventWithXOrigin` (geckoview-junit-fis / -nofis jobs).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". The geckoview-junit harness is not indexed at all.
- Workaround: a script over the Treeherder API (`/api/jobs/?push_id=..&job_type_name=..` then `/jobs/<id>/text_log_errors/`), grepping for the test name. That took about 15 minutes of wall time and several attempts: `count=2000` truncates big pushes silently, and filtering by `job_type_name` was the fast path.
- Question it could not answer: "how often does this junit test fail per config, and since when". `fx-tests intermittent --bug` gives only the sheriff-annotated count (56 in 7 days). The real count is 83 failures in 551 fis runs.

## `fx-tests task` says a junit job's profile records no tests

- Command: `fx-tests task b6Emm2vLQ26LQMGEYbME2w --profiles`
- Expected: the job's test list, with `PanZoomControllerTest#touchEventWithXOrigin` as FAIL.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus a warning that "128 failing markers in this job named no test path". The resource-usage profile does hold 1457 `test` markers, and one of them is `FAIL — org.mozilla.geckoview.test.PanZoomControllerTest#touchEventWithXOrigin`. The `Class#method` names are apparently not recognised as tests.
- Workaround: loaded the resource-usage profile in profiler-cli and searched the `test` markers.
