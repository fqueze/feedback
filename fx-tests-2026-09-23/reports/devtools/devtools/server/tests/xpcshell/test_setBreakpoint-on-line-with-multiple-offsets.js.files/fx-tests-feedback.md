## `fx-tests test <path> --bugs` printed nothing about bugs

- Command: `fx-tests test devtools/server/tests/xpcshell/test_setBreakpoint-on-line-with-multiple-offsets.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`, with no bugs line at all, so "none found" and "flag ignored" look the same.
- Workaround: took it as "none"; found the job-level bug 1991833 from sibling reports and `fx-tests intermittent --bug`.
