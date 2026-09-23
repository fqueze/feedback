## `fx-tests test` reports a TEST-KNOWN-FAIL as the failure

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_grid-highlighter-restored-after-reload.js`
- Expected: the Issues row to show the message that made the run fail (`TEST-UNEXPECTED-FAIL`): `uncaught rejection: Connection closed, pending request to server0.conn0.watcher2.processN//gridNN, type getFragments failed`.
- Got: `17x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... js-toggle-grid-highlighter`. In the profile that message is `TEST-KNOWN-FAIL` (Status FAIL, Expected FAIL, emitted by `test_todo` from `AccessibilityUtils.js!a11yWarn`), i.e. an expected todo, not a failure. It comes first in time, so "first failure per run" picks it. `fx-tests task <id> --messages` lists both with the same `1x` weight and no status, so nothing tells them apart either.
- Workaround: loaded a profile and read the TestStatus markers' `Expected` field; then ran `fx-tests task <id> --messages` for all 17 tasks to confirm every one had the `getFragments` rejection.
- Question the output should have answered: "what was the unexpected failure of each run". Excluding Expected==Status results (known-fail / todo) from Issues, or labelling them, would have.

## `fx-tests test --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_grid-highlighter-restored-after-reload.js --bugs`
- Expected: a "Bugs" section, saying "none" when empty.
- Got: output identical to the run without `--bugs`, so "no bug" looks like "flag ignored". `--json` has `annotatedBugs: []`.
- Workaround: `--json` and read `annotatedBugs`.
