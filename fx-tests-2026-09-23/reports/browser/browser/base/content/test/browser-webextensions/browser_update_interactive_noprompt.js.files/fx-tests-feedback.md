# fx-tests feedback

## An expected `todo` (TEST-FAIL, expected FAIL) is reported as the failure

- Command: `fx-tests test browser/base/content/test/webextensions/browser_update_interactive_noprompt.js`
  (same with the `browser-webextensions/` path, and in `fx-tests task <id>` for every failing job).
- Expected: Issue 1 = `Shutdown - leaked window until shutdown [url = about:addons]`, 27x — the
  only unexpected result in every failing run.
- Got: Issue 1 = `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: BUTTON`
  24x, and the leak only 1x. That line is `SimpleTest.todo()` from `AccessibilityUtils.a11yWarn`,
  logged as `TEST-FAIL` with expected `FAIL` (a known fail, counted under "Todo: 2" in the
  harness summary), and it is emitted by every run, passing ones too. It only shows in failing
  runs because the harness dumps the buffered messages when a test leaks.
- Workaround: `fx-tests task <id> --messages` lists all three messages; reading the raw log
  showed which one was unexpected.
- Cost: the first guess from the Issues list was an a11y-checks failure, which is a different
  diagnosis entirely.
