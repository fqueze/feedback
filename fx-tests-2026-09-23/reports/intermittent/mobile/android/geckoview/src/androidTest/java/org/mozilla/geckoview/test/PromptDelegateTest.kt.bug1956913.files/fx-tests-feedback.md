## geckoview-junit: no rates, no denominator (same gap as the bug1961358 and AutocompleteTest reports)

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/PromptDelegateTest.kt`
  - Expected: per-config rates, or "junit is not indexed".
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
  - Workaround: `fx-tests intermittent --bug 1956913 --since 90 --limit 0` for failures; the Treeherder jobs API (`/api/project/autoland/jobs/?job_type_name=...&push_id__gte=...`) for the run counts, and downloading each failed job's `live_backing.log` to see whether the test failed after the fix.
- Command: `fx-tests task UZAWfNbXSX-u2ltzW0g_1w --profiles` (ID copied from `fx-tests intermittent`, which prints `UZAWfNbXSX-u2ltzW0g_1w.1`, with the `.1` dropped)
  - Got: "the artifact is not there ... this is permanent". Run 0 was an infra exception; run 1 has the profile. The message does say `.0` is assumed, but "permanent" sent me the wrong way first.
  - Could have said: "run 0 is an exception; runs 1 exists — try `<id>.1`".
