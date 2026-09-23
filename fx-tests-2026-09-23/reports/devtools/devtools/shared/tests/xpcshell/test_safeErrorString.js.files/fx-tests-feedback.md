## Question: which bug were this test's failing jobs starred on?

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_safeErrorString.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs starred all 3 failing jobs with.
- Got: no Bugs section at all (the bug does not name the test).
- Workaround: Treeherder API `jobs/?task_id=...` then `bug-job-map/?job_id=...` for each failing task.
- What could show it: `--bugs` (or `--task-ids`) listing the bug each failing job was classified with, not only bugs whose summary names the test.

## Question: when did this job break down, and which tests were running at that moment?

- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ.0 --profiles` (750 TIMEOUTs out of 920 tests)
- Expected: a hint that this is a job-wide breakdown (747 tests "started" within 3 s with a 20-thread limit, all TIMEOUT 45 s later), the time it began, and the harness exception that ended the run (`PermissionError: [WinError 5] Access is denied` from `launchProcess`, only in an ERROR marker of the resource-usage profile).
- Got: 750 per-test rows each saying "Test timed out"; nothing about the breakdown or the traceback.
- Workaround: `profiler-cli thread markers --search name:test --list --limit 0 --json` + a Python script bucketing test statuses by start time, and `--search Traceback`.
- What could show it: `fx-tests task` flagging "N tests failed within X s starting at t=..., first failing test ..., harness traceback: ..." when most of a job fails at once.
