## Expected TEST-FAIL (todo) results are listed as failure modes

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_switcher_telemetry.js` (Issues), and `fx-tests task Yi5LLfgIQ1m0bUEzleERSw --messages`.
- Expected: failure modes built from unexpected results only.
- Got: Issue 5 is `FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`, and `--messages` lists `failed to upload profile: ...`. In the log both are `INFO - TEST-FAIL | ...`, i.e. expected FAIL (browser-test.js reports `changed preference` with `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`, and CI does not pass `--compare-preferences`). In Yi5L the real failures are `uncaught rejection: out of memory` and the CC-graph OOM crash.
- Cost: had to fetch the 13 MB live log and read browser-test.js + SpecialPowersParent to learn the pref message was not a real failure.
- Workaround: grep the log for `TEST-UNEXPECTED` / `PROCESS-CRASH`.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test <path> --bugs`
- Expected: a line such as "No bug names this test".
- Got: the same output as without `--bugs`, so "none found" and "flag ignored" look the same.
- Workaround: Bugzilla REST quicksearch.
