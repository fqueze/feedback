## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_pause_exceptions-04.js --bugs`
- Expected: a line such as `Bugs: none name this test` (and ideally the job-level bug the failing jobs were starred with, here 1991833).
- Got: exactly the output of the command without `--bugs`; no bug section at all, so "none found" and "flag ignored" look the same.
- Workaround: took the bug from a sibling report and confirmed with `fx-tests intermittent --bug 1991833`.
