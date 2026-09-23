## Question: what actually failed this test? (`fx-tests test`'s Issues block names a known-fail)

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js`
- Expected: Issues ranking the unexpected failures that made runs fail.
- Got: row 1, `1084x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime` (and row 5, `changed preference: browser.crashReports.cleanupCheck.lastDate`). In CI `--compare-preferences` is off, so `browser-test.js` `checkPreferencesAfterTest` records these as `todo` results with status FAIL and expected FAIL: a known fail, not a failure. The real failure in those runs, `TEST-UNEXPECTED-FAIL | This test exceeded the timeout threshold ... limit was 180s (timeout factor 2)`, appears nowhere in `fx-tests test`'s output, `--json` included (`issues` and `messages` both lack it). It is the whole perma-fail on asan, linux debug and ccov.
- Workaround: `fx-tests task <id> --messages` on a sample of the row-1 tasks, one at a time.
- What would have answered it: skip results whose expected status is FAIL (or todo) when picking a run's "first failure", or show the first *unexpected* message.
