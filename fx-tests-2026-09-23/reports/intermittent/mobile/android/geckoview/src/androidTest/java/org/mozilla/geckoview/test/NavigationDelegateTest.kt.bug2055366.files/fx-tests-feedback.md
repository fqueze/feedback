## GeckoView junit tests are invisible to `fx-tests test` / `fx-tests task`

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/NavigationDelegateTest.kt`
- Expected: rates per config, or a hint that junit is not covered.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — reads as if the test never ran.
- Command: `fx-tests task NQgpjAJ1Tz6sDajdWY5CGw --profiles`
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", while the job's
  resource-usage profile holds 1457 `test` markers (one FAIL for `NavigationDelegateTest#locationReplaceOnUserGesture`).
- Workaround: downloaded `live_backing.log` and `public/test_info/logcat-emulator-5554.log` per task by hand;
  run counts (the denominator for a failure rate) were not obtainable at all.
- Question it could have answered: "how often does this junit test fail, on which configs, out of how many runs?"
