## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_pause_exceptions-01.js --bugs`
- Expected: a Bugs section, saying "no bug names this test" when there is none.
- Got: the same output as without `--bugs`; no line about bugs at all, so "none found" cannot be told from "flag ignored".
- Workaround: took it as none, and got the msix bug (1991833) from sibling reports.
- Question it blocked: "which bug covers this failure?" Here the failures carry no test-naming line, so a pointer to the bug starred on these task IDs would have answered it.
