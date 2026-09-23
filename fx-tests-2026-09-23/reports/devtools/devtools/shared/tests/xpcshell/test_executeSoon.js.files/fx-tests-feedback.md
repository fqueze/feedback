## Question: which bug are this test's failures starred on?
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_executeSoon.js --bugs`
- Expected: the bug(s) sheriffs starred the 3 failing jobs on, or an explicit "no bug found" line.
- Got: the normal output with no bugs section and no "none" line; `--json` has `annotatedBugs: null`. The jobs are starred on bugs that do not name the test (bug 1991833 "High frequency win-msix PermissionError: [WinError 5] Access is denied", bug 2016044 msix tracking bug), found only by Bugzilla quicksearch.
- Could have shown: the bugs annotated on the failing task IDs (Treeherder job classifications), even when their summary does not name the test.

## Question: is this a job-wide break, and which other jobs on this config had it?
- Command: `fx-tests task <taskId> --profiles` showed 750-1564 TIMEOUTs per job (good), but finding the other jobs with the same break meant running `fx-tests test` on unrelated victim tests (test_add_actors.js, test_cookies_async_failure.js) with `--config ... --task-ids`.
- Could have shown: in `fx-tests test`, a flag on each failing task when most of the job failed ("job-wide: 750 of 920 tests failed"), and a way to list all jobs of a config with such a mass failure.
