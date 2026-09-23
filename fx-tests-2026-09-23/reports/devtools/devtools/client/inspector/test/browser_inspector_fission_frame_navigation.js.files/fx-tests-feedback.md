## `test --bugs` prints no bug section (same as browser_rules_at_scope.js's feedback)

- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_fission_frame_navigation.js --bugs`
- Expected: the bugs naming the test, or an explicit "none" (Bugzilla has 4, all RESOLVED).
- Got: the same output as without `--bugs`.
- Workaround: Bugzilla REST `bug?summary=browser_inspector_fission_frame_navigation`.

## `errors --message … --limit 0` still truncates the test list (same as browser_rules_at_scope.js's feedback)

- Command: `fx-tests errors --message "crashed without leaving a minidump behind" --day 2026-09-17 --limit 0`
- Expected: all 15 tests. Got: 5 and `… 10 more tests`.
- Workaround: `--group-by test --limit 0`.

