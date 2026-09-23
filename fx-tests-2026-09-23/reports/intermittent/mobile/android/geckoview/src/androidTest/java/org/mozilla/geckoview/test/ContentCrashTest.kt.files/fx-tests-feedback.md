## `fx-tests task` says a geckoview-junit resource profile "records no tests at all" when it holds 135 test markers

- Command: `fx-tests task HxyQywLwRIyT3NyRR7wSVg --profiles`
- Expected: the job's tests and their outcomes, including `org.mozilla.geckoview.test.ContentCrashTest#crashContentJava` FAIL.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", plus a warning that 3 failing markers "named no test path". The same profile, loaded in profiler-cli, has 135 `test` markers with statuses (PASS/FAIL), so the harness did run tests; they are just named `Class#method`, not paths.
- Workaround: loaded the resource-usage profile in profiler-cli and read the `test` markers directly; read live_backing.log and the logcat artifact for the rest.
- Suggestion: treat `Class#method` test markers (geckoview-junit) as tests, or at least say "N test markers without a path" instead of "no tests at all", which reads as "the harness died first".

## Question: "the tests behind this bug" for a suite `fx-tests test` does not index

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/ContentCrashTest.kt`
- Got: exit with "No test path in the xpcshell and mochitest 21-day data contains ...". Expected for junit, but it means no run count, so no failure rate, for a bug whose annotations are all geckoview-junit.
- Workaround: `fx-tests intermittent --bug 1993445 --since 21 --limit 0 --json`, and a Python script over `occurrenceRows` to split the failure modes by build type (the text output lists messages and build types separately, so "which build type does each message come from" needed the JSON: NPE = debug-isolated-process only, timeouts = opt only).
- What would have answered it: a per-message breakdown by build type/platform in `intermittent --bug`'s text output.
