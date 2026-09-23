## Question: "which bug were this test's failing jobs starred with?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-08.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs starred job LKys8j8iTQKTAN9hKv040g with.
- Got: nothing, since that bug's summary names no test.
- Workaround: Bugzilla quicksearch on the error text, then `fx-tests intermittent --bug 1991833` to see the task ids.
- The failing task ids are known to `fx-tests test --task-ids`; listing the bug each was annotated with would answer it.

## Question: "was this failure part of a job-wide meltdown?"

- Command: `fx-tests test <path> --task-ids`, then `fx-tests task <id>` for each of the 6 tasks.
- All 6 jobs had 450-1,560 TIMEOUTs each; that is the key fact for this test, and it took one `task` call per job to see it. A per-task "failures in this job" count next to each task id in `--task-ids` would show it at once.
