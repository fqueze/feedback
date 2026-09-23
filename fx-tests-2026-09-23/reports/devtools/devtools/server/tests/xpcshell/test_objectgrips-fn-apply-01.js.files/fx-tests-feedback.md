## Which bug were this test's failing jobs starred on?
- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-fn-apply-01.js --bugs` (and `--json`: `annotatedBugs` is null); `fx-tests intermittent --test <path>` says no bug names the test.
- Expected: the bug(s) sheriffs starred the failing jobs with. All 6 failing jobs are starred on bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), whose summary names no test.
- Got: nothing, so it looked like no bug existed.
- Workaround: Treeherder API per job, `api/project/autoland/jobs/?task_id=<id>` then `bug-job-map/?job_id=<id>`.
- What would answer it: `--bugs` (or `--task-ids`) listing the bug-job-map bug of each failing task, even when the bug summary names no test.

## How did my test fail in this job, and what else failed that was not a timeout?
- Command: `fx-tests task <taskId> --profiles` on a job with 477 failing tests.
- Expected: a way to jump to one test (`--test <path>`), and to see the few non-TIMEOUT failures in a mass-timeout job.
- Got: 477 entries in manifest order; I had to script over `--json` to pull out my test's entry and the FAIL entries.
- What would answer it: `fx-tests task <id> --test <path>` and a `--status FAIL` filter, or a summary line such as "476 TIMEOUT, of which 475 started within 2 s of each other" pointing at a job-wide breakage.
