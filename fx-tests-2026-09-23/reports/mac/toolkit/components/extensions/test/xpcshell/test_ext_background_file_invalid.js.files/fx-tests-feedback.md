## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_background_file_invalid.js --bugs`
- Expected: a line such as `Bugs: none name this test` (or the list).
- Got: exactly the same output as without `--bugs`, no bugs section at all, so "no bug" and "flag ignored" look identical.
- Workaround: `--bugs --json` and read `annotatedBugs` (`[]`), then a Bugzilla summary search to confirm.
