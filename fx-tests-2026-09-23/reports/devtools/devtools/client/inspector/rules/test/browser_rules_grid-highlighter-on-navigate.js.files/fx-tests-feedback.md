# fx-tests feedback

## A TEST-KNOWN-FAIL (todo) is reported as the failure

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_grid-highlighter-on-navigate.js`
- Expected: "Issues (first failure per run)" to show the TEST-UNEXPECTED-FAIL of the run:
  `uncaught rejection: Connection closed, pending request to server0.conn0.watcher2.process4//grid36, type getFragments failed`.
- Got: `135x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... inspector-grid js-toggle-grid-highlighter`.
  In the profile this message is a `TEST-KNOWN-FAIL` marker: AccessibilityUtils' `a11yWarn` is a
  `SimpleTest.todo(false, ...)`, so it is expected, not a failure. It is logged before the real
  failure, so it wins "first failure per run" on every one of the 135 runs, and hides the real
  message entirely from the `test` view. `fx-tests failures --message "unable to perform a11y checks on hidden node"`
  likewise ranks thousands of these todos (2,633 in browser_sync.js) as failures.
- Workaround: `fx-tests task <id> --profiles --messages` shows the second message, which is the real one.
- Suggestion: drop TEST-KNOWN-FAIL / todo results from failure messages.
