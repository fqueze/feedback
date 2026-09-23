# fx-tests feedback (bug 1732236, geckoview-junit)

1. `fx-tests task I8pKGM95SaevM8S5Arrttw --profiles`
   - Expected: the failing run, or a note that run .0 was an `exception` (worker-shutdown) and run .1 is the failed one.
   - Got: "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent" — the task is 1 day old; run .0 simply never uploaded one because it was an exception run.
   - Workaround: `fx-tests intermittent --bug N` lists `.1`; pass the run suffix explicitly.

2. `fx-tests task I8pKGM95SaevM8S5Arrttw.1 --profiles` (geckoview-junit job)
   - Expected: the junit tests of the job, or a clear "junit is not supported".
   - Got: "0 tests, 0 executions, 0 failing" plus "19 failing markers in this job named no test path ... FAIL org.mozilla.geckoview.test.AccessibilityTest#testClipboard ...". Those 19 are JUnit assumption failures (`INSTRUMENTATION_STATUS_CODE: -4`, counted as "Todo" by runjunit.py), not failures; the real failure (`Some tests did not run`) is not among them.
   - Workaround: read live_backing.log and logcat-emulator-5554.log directly.

3. `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/ProfileLockedTest.kt`
   - Question: "how often does this junit test crash the run, per config, and since when?"
   - Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — geckoview-junit is not indexed, so no rates, history or task ids for junit tests.
   - Workaround: Treeherder `failurecount` / `failuresbybug` APIs for the bug.
