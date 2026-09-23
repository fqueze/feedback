## `fx-tests test <path>` does not say its failures all come from jobs that broke wholesale

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_flatten.js --task-ids --limit 0`
- Expected: some hint that each of the 3 failing jobs had 750-1560 other failing tests (a job-wide breakdown, here bug 1991833), e.g. "3 of 3 failures in jobs where >50% of tests failed".
- Got: "Verdict: intermittent ... 3x TIMEOUT Test exceeded time limit", which reads like a slow test. Only `fx-tests task <id>` for each job revealed "1061 failing" / "1564 failing" / "755 failing".
- Workaround: ran `fx-tests task` on every failing task.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_flatten.js --bugs`
- Expected: a "Bugs: none name this test" line (and ideally the bug sheriffs starred these jobs with: 1991833).
- Got: the normal output with no Bugs section at all, indistinguishable from the flag being ignored.
- Workaround: Bugzilla quicksearch for "WinError 5 msix".
