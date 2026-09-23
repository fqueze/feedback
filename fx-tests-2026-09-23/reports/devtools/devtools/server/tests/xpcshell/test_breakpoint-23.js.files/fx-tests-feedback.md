## `fx-tests test <path> --bugs` prints nothing about bugs when none is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_breakpoint-23.js --bugs`
- Expected: a Bugs section, either listing bugs naming the test, or saying explicitly that none does.
- Got: exactly the same output as without `--bugs` (exit 0), so "no bug names this test" and "the flag was ignored" look identical.
- Workaround: took the msix bug (1991833) from a sibling report, then `fx-tests intermittent --bug 1991833`.
