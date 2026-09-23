## `fx-tests test --bugs` prints nothing when no bug names the test
- Command: `fx-tests test devtools/shared/protocol/tests/xpcshell/test_protocol_children.js --bugs`
- Expected: a line saying no bug names the test, and ideally the bugs matching the failure (here bug 1991833, "win-msix PermissionError: [WinError 5] Access is denied", which is what the job's traceback says).
- Got: the same output as without `--bugs`, with no Bugs section at all.
- Workaround: Bugzilla quicksearch on the job's traceback text.

## Question: "is this failure a job-wide collapse?" (needed a script over `task --json --passed`)
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg` reports "1152 TIMEOUT, 45 PASS". It says nothing about those TIMEOUTs being launch failures: each test's thread ended about 18 ms after TEST-START ("will retry"), and the harness's 45 s kill timer then reported a TIMEOUT for a process that never existed ("not killing -- proc or pid unknown").
- Could have shown: "N tests failed within X s of starting; the first failure at t=…; the job's final exception: PermissionError…". The `Following exceptions were raised` traceback is the single most useful line in such a job.

## Question: "which jobs share this collapse, and where in the test order does it hit?" (needed a script over `task --json --passed`, per job)
- Commands: `fx-tests test <other test> --task-ids --limit 0` for two tests, to collect 19 msix job ids; then `fx-tests task <id> --json --passed` per job, filtered for a handful of test paths.
- Could have shown: for a failure message or signature, the jobs that had it, with each job's PASS/TIMEOUT counts and the last tests that passed before the first failure.
