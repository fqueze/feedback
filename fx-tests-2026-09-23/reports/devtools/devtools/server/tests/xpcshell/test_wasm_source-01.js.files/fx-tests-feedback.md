## Question: were this test's failures part of job-wide breakdowns?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_wasm_source-01.js --task-ids --limit 0`
- Expected: some sign, per failing task, that the job itself collapsed (all 6 failing jobs had 476 to 1,563 other tests TIMEOUT).
- Got: 6 task IDs and "6x TIMEOUT Test exceeded time limit", which reads like a slow test. I only found out by running `fx-tests task <id> --profiles` on each of the 6.
- Workaround: `fx-tests task` per task, `head -7` for the outcome counts.
- Could have shown: next to each task ID, the number of tests failing in that job (e.g. "1,061 failing of 1,472"), and a verdict line when most failures come from mass-failure jobs.

## `fx-tests test --bugs` found no bug; the bug exists

- Command: `fx-tests test devtools/server/tests/xpcshell/test_wasm_source-01.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which is what these jobs are starred on (LKys8j8iTQKTAN9hKv040g is in `fx-tests intermittent --bug 1991833`).
- Got: nothing (the bug does not name the test, and the job's annotation carries no TEST-UNEXPECTED-FAIL line).
- Workaround: Bugzilla quicksearch on the traceback text.
- Could have shown: bugs sheriffs starred the failing task IDs with, not only bugs naming the test.
