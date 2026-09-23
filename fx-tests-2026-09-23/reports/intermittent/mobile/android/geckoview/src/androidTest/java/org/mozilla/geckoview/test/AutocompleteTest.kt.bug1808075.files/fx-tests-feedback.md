## geckoview-junit: no run counts, and `task` says the job ran no tests

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AutocompleteTest.kt`
  - Expected: per-config rates, or "geckoview-junit is not indexed".
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (reads like a typo, not a coverage gap).
  - Workaround: `fx-tests intermittent --bug 1808075 --since 90 --history --limit 0` for annotation counts and task ids. There are no run counts, so no failure rate.
- Command: `fx-tests task AFNemMtZQmy3dKoGybUQYw --profiles`
  - Expected: `AutocompleteTest#creditCardSelectDismiss` as the job's unexpected failure.
  - Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", though the resource-usage profile has 1457 `test` markers (named `Class#method`, no path).
  - Workaround: grep `TEST-UNEXPECTED` in `live_backing.log`.

## Question: "does every occurrence of this bug show the same log sequence?"

- Needed: for each of a bug's 41 annotated jobs, the failing test's logcat window, grepped for a few lines (`Error while prompting`, `Prompt:Dismiss`, `handleEvent Autocomplete:Select:CreditCard`), with timings relative to one of them.
- Did: took the task ids from `fx-tests intermittent --bug 1808075 --since 90 --limit 0`, downloaded 41 `live_backing.log`, and wrote `summarize.py` over them.
- `fx-tests intermittent --bug` prints "log lines", but only the harness's summary line. An option to grep each occurrence's failing-test log window (from `test_start` to `TEST-UNEXPECTED-FAIL`) for a pattern would have answered the question in one command.
