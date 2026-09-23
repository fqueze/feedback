## Question: which bug are this test's failing jobs starred on? (test_stepping-with-skip-breakpoints.js, 2026-09-22)

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-with-skip-breakpoints.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: exactly the same output as without `--bugs`, with no Bugs section and no "none" line. I could not tell "no bug found" from "flag ignored".
- The bug that matters here, 1991833, does not name the test: sheriffs starred all 6 failing jobs on it. I found it only from a sibling report, then confirmed it with `fx-tests intermittent --bug 1991833 --since 25 --limit 0`, grepping for the 6 task IDs. It would help if `--bugs` (or `--task-ids`) printed the bug each failing job was starred on.
