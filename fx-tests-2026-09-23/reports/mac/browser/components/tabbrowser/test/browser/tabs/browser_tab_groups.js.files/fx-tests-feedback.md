## `fx-tests test` names a TEST-KNOWN-FAIL (todo) as the failure

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js`
- Expected: "Issues (first failure per run)" to show the TEST-UNEXPECTED-FAIL that made the run
  fail: `arrowscrollbox is still scrolled to start - false == true`.
- Got: `101x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: , tagName:
  label, className: tab-group-label`. That message is `SimpleTest.todo(false, ...)`
  (AccessibilityUtils `a11yWarn`), logged as TEST-KNOWN-FAIL in the profile (m-16 in
  FxRLUlOtTRS9Y2umHDZGIA). It is emitted before the real failure and on every run on these
  configs, so it wins "first message" but is not a failure. The two `changed preference: ...`
  messages listed by `fx-tests task --messages` are also TEST-KNOWN-FAIL. `--issue 1` therefore
  groups the failures under a message that is not a failure at all, which sends the reader to
  the wrong subtest.
- Workaround: `fx-tests task <id> --messages --full-messages` on several tasks, then the profile's
  Test markers to see which message is TEST-UNEXPECTED-FAIL.
- Would help: exclude TEST-KNOWN-FAIL / todo messages from failure messages and from issue
  grouping, or tag each message with its status in `task --messages`.
