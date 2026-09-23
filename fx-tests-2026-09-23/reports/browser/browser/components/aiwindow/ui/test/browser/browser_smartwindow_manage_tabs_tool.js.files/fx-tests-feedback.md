## `fx-tests test` Issues row is a TEST-KNOWN-FAIL, not the failure

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_manage_tabs_tool.js`
- Expected: Issue 1 to be the message that made the runs fail: `TEST-UNEXPECTED-FAIL | This test exceeded the timeout threshold ... Test ran for 104s, limit was 90s`.
- Got: `1. 1110x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`. The profile shows that message as `TEST-KNOWN-FAIL` (browser-test.js `checkPreferencesAfterTest`, `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`), and the tests before it in the same browser log it too without failing. `fx-tests task <id> --messages` also lists it with the real failures, all under `FAIL`, so nothing shows which one was unexpected.
- Workaround: `fx-tests task <id> --messages`, then read the profile's `TEST-UNEXPECTED-FAIL` marker. The sheriffs' `fx-tests intermittent --bug` view had the right message.
- Suggestion: leave known-fail/todo results out of "first failure per run", or at least label them.

## `fx-tests task` does not say how long the test ran

- Question: "how long did this test run in each execution, against its timeout?" For a "too slow for its timeout" failure the run time is the whole answer.
- Got: `--messages` rewrites `Test ran for 104s` to `Test ran for Xs`, and does not print durations. I had to load both per-test profiles and the resource-usage profile to get 104 s / 96 s (Windows ASan) and 129 s / 86 s (Linux ASan, the retry passed).
- Suggestion: print each execution's duration next to its status in `fx-tests task`.
