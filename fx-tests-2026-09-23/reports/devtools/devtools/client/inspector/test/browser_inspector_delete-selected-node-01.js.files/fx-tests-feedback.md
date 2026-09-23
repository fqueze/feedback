## `errors --limit 0` does not expand the per-message test list

- Command: `fx-tests errors --day 2026-09-18 --message "crashed without leaving a minidump behind" --limit 0`
- Expected: the "N occurrences in M tests" block to list all 16 tests.
- Got: 5 tests, then `… 11 more tests`, with no flag named to show them.
- Workaround: `--group-by test --task-ids`, which lists them all.

## `test <path> --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_delete-selected-node-01.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, so I could not tell whether it searched.
- Workaround: searched Bugzilla directly.
