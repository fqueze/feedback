# fx-tests feedback

## Question: what is the unexpected failure behind these failing runs?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js`
- Expected: "Issues (first failure per run)" to list the first *unexpected* failure of each run.
- Got: `1084x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime` and
  `74x FAIL changed preference: browser.crashReports.cleanupCheck.lastDate` as the top issues.
  In the profile these are `TEST-KNOWN-FAIL` (browser-test.js records them with
  `todo: !gConfig.comparePrefs`), they never turn a job orange. The actual most frequent failure,
  `This test exceeded the timeout threshold...` (every asan/ccov run, most linux debug runs), does
  not appear in Issues at all, because a known-fail pref message always precedes it.
- Workaround: `fx-tests task <id> --messages` per task, then the profile's Test markers.
- Could have shown: skip todo/known-fail results when picking the "first failure", or show the
  first TEST-UNEXPECTED-* message.

## Question: how long did the failing runs take, against which limit?

- Command: `fx-tests task O02_k9H2QGSuG85fW4AcPw --messages --full-messages`
- Expected: `Test ran for 255s, limit was 180s (timeout factor 2).`
- Got: `Test ran for Xs, limit was 180s (timeout factor 2).` The normalization that groups
  messages also erases the one number that matters, even with `--full-messages`.
- Workaround: `curl` the task's `live_backing.log` and grep `Test ran for`.
- Could have shown: the raw message (or the numbers) when `--full-messages` is given for one task.
