## geckoview-junit tests are not in `fx-tests test`, and `fx-tests task` sees 0 tests in a junit job

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/PromptDelegateTest.kt`
  - Expected: rates per config, or "junit is not indexed".
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ...", which reads as a typo. (Same as already logged by the AutocompleteTest report.)
  - Workaround: `fx-tests intermittent --bug 1961358 --since 90 --history --limit 0`, and for a denominator the Treeherder jobs API (`/api/project/autoland/jobs/?job_type_name=...&push_id__gte=...`).
- Command: `fx-tests task WkWCjrpwTZqXCYlkP6fxCQ --profiles`
  - Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus "138 failing markers in this job named no test path". The resource profile has 1457 `test` markers named `Class#method`.
  - Question the default output could have answered: "which junit tests failed in this job, and what ran just before". Workaround: grep `TEST-UNEXPECTED` in `live_backing.log`.
- Question with no command: "how many runs of this junit job happened after the fix landed, and did the test pass in them" (to decide `no longer fails`). Workaround: Treeherder pushes/jobs API plus downloading each log.
