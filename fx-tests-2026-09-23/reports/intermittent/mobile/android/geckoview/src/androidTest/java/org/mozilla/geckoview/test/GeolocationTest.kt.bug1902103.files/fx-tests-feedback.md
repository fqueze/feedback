## `fx-tests test` has no geckoview-junit data

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/GeolocationTest.kt`
- Expected: rates per config and failure modes for a GeckoView junit test (bug 1902103, 37 sheriff annotations in 60 days).
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. No run counts, so no failure rate can be computed for any junit test.
- Workaround: `fx-tests intermittent --bug 1902103 --since 60 --history --limit 0` for annotated failures; Treeherder `/api/jobs/?push_id=` to find green jobs to compare with; no denominator.

## `fx-tests task` says a geckoview-junit job ran no tests

- Command: `fx-tests task PIKpGqhRRBOe6UsD63x3LA --profiles`
- Expected: the job's test outcomes; the resource-usage profile holds 1457 `test` markers, one of them `FAIL — org.mozilla.geckoview.test.GeolocationTest#jsContentRequestForLocation`.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", plus a warning that 138 failing markers named no test path (these are junit `Class#method` names, and most are expected-crash tests, not failures).
- Workaround: loaded the resource-usage profile in profiler-cli and searched the `test` markers by name.
