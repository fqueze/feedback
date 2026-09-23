## Question: which bug are this test's failing jobs starred on?

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_toFixed.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs starred all 3 msix failing jobs on.
- Got: no bug, because the bug summary names no test.
- Workaround: Treeherder API `api/project/autoland/jobs/?task_id=<id>` then `bug-job-map/?job_id=<id>`.
- What would have answered it: `--task-ids` (or `--bugs`) listing the classification bug of each failing task.

## Question: what made 1000+ tests fail in this job?

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Expected: a job-level line saying that the harness raised `PermissionError: [WinError 5] Access is denied` from `launchProcess`, and that the TIMEOUTs are tests never launched.
- Got: 1061 per-test rows, each "Test timed out" / "Timed out and was force-killed by the harness"; the harness traceback only found by loading the resource-usage profile.
- Workaround: profiler-cli search for `Traceback` / `Following exceptions were raised`.
