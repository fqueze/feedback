## GeckoView junit tests: no denominator anywhere (AccessibilityTest.kt, bug 2066778)

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AccessibilityTest.kt`
- Expected: rates per config for the junit test, or a pointer to where junit data lives.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `fx-tests intermittent --bug 2066778` gives the 27 failures, but nothing gives the number of runs.
- Workaround: a Python script over the Treeherder `/api/project/<repo>/jobs/?job_type_name=...&push_id__gte=...` API, one call per platform x build x suite, counting success + testfailed jobs (`count_jobs.py`, output in `job_counts.txt`).
- Question it could have answered: "what is this junit test's failure rate per config over the bug's window?" `intermittent --bug` could print runs next to failures per config, since it already knows the job names.

## `fx-tests task` says a geckoview-junit job has no tests when its profile has 1457

- Command: `fx-tests task HXQ40vINTHCxON7LOcl3DQ --profiles`
- Expected: the job's tests, with the failing one.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", plus "129 failing markers in this job named no test path". The resource-usage profile has 1457 `test` markers with Status PASS/FAIL/SKIP (junit names like `org.mozilla.geckoview.test.AccessibilityTest#testForceEnabledByPrefSurvivesNewSessionAttach`, no file path).
- Also: the "failing" list includes `AccessibilityTest#testClipboard`, an `AssumptionViolatedException` (a skip) that the junit harness logs as `TEST-FAIL`; the job had only 2 `TEST-UNEXPECTED-FAIL`.
- Workaround: loaded the profile in profiler-cli and searched the test name.
