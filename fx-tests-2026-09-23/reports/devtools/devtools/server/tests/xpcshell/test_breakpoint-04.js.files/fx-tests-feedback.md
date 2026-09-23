## Question: "does this job's failure of my test look like a job-wide collapse, and where is my test in it?"
- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles`
- Expected: the entry for the test I came from (test_breakpoint-04.js), or a filter such as `--test <path>`.
- Got: the first ~20 of 477 failing tests, alphabetical; my test was not among them. Had to rerun with `--limit 0` (1,900+ lines) and grep.
- Workaround: `--limit 0 > file; grep -A4 <test>`. A `--test <path>` filter, or always printing the requested test's row, would answer it directly. The header line "476 TIMEOUT" did flag the collapse well.

## Question: "which bug were the jobs where this test failed starred with?"
- Command: `fx-tests test devtools/server/tests/xpcshell/test_breakpoint-04.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs star these jobs with.
- Got: no bug. The job-level bug does not name the test, so it is invisible from the test's side.
- Workaround: Bugzilla quicksearch for "msix". Showing the bugs the failing task IDs were annotated with would have found it at once.
