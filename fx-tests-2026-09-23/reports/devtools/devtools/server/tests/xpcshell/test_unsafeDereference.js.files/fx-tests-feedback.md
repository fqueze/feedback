## Which bugs name this test?
- Command: `fx-tests test devtools/server/tests/xpcshell/test_unsafeDereference.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, no bug section at all, so "none found" and "flag ignored" look identical.
