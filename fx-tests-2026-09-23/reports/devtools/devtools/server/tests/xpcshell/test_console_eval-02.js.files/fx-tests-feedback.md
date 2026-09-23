## test_console_eval-02.js (2026-09-22)

### Question: which bug are this test's failing jobs starred against?
- Command: `fx-tests test devtools/server/tests/xpcshell/test_console_eval-02.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs starred all 6 failing jobs against.
- Got: no bug section at all, because that bug's summary does not name the test.
- Workaround: Treeherder `api/project/autoland/jobs/?task_id=<id>` then `bug-job-map/?job_id=<id>`, once per task.
- What could have shown it: `--bugs` (or `--task-ids`) could list the bug each failing job is classified against, as well as bugs whose summary names the test.

### Question: is this TIMEOUT a real timeout, or part of a job that collapsed?
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: some sign that 487 of 550 tests timing out means the job collapsed, and that those "TIMEOUT"s are tests the harness could not even launch.
- Got: a list of 488 failing tests, all TIMEOUT, with nothing to say they belong together.
- Workaround: loaded the resource-usage profile and matched the count of "not killing -- proc or pid unknown" messages to the count of 45 s TIMEOUTs with a script over `profiler-cli --json` (747/747 and 1149/1149).
- What could have shown it: `task` could flag jobs where most tests fail the same way, and name the first failure in time (here `[GFX1-]: GPU proc launch error SB::LA::SpawnTarget 0x80073cfc`, then the harness traceback `PermissionError: [WinError 5] Access is denied`). `test` could also count a test's failures that come from such jobs separately under Issues.
