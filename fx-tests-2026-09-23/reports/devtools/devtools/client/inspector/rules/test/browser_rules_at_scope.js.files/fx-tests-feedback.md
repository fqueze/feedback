## `errors --limit 0` does not expand a message's test list

- Command: `fx-tests errors --message "crashed without leaving a minidump" --day 2026-09-15 --limit 0`
- Expected: every test behind the message.
- Got: 5 tests and `… 7 more tests`, even with `--limit 0`.
- Workaround: `--group-by test --limit 0`, which lists them all (and `--task-ids`).

## `test --bugs` prints no bug section

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_at_scope.js --bugs`
- Expected: the bugs naming the test, or an explicit "none". Bugzilla has bug 1903426 (RESOLVED INCOMPLETE, "Intermittent …browser_rules_at_scope.js | single tracking bug").
- Got: the same output as without `--bugs`, with no line about bugs at all, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST `bug?summary=browser_rules_at_scope`.
