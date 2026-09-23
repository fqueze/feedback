## geckoview-junit tests are invisible to `fx-tests test` and `fx-tests task`

- Question: "how often does this GeckoView junit test fail, and on which configs?"
- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AutocompleteTest.kt`
- Expected: rates per config (the test runs in geckoview-junit on android-em-14).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — the harness is not covered at all.
- Command: `fx-tests task PBrOfDx6SGCfHV5Y5EUzuw --profiles`
- Expected: the job's test outcomes; the resource-usage profile holds 1457 `test` markers with Test Name / Status (FAIL for the 3 failing tests).
- Got: "0 tests, 0 executions, 0 failing" and "136 failing markers in this job named no test path", then "This profile records no tests at all".
- Workaround: `fx-tests intermittent --bug N` for the occurrences, Treeherder `api/failurecount` for the rate, then the job logs and logcat artifacts by hand.
