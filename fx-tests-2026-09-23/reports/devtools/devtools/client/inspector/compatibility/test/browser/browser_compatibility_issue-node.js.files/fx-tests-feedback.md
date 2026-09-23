## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/inspector/compatibility/test/browser/browser_compatibility_issue-node.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: exactly the same output as without `--bugs` (stdout and stderr), so "no bug" cannot be told apart from "flag ignored" or "lookup failed".
- Workaround: none; took it as "no bug", cross-checked against other reports.
