## `fx-tests test <path> --bugs` prints no bug section when none is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_framebindings-08.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the normal `test` output with no mention of bugs, so it is unclear whether the flag was honoured.
- Workaround: took the bug from the sibling report and `fx-tests intermittent --bug 1991833`.
