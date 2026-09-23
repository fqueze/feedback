## test_listsources-02.js diagnosis (2026-09-22)

### Question: "is this failure the test's own, or one of hundreds in a job that melted down?"
- Command: `fx-tests test devtools/server/tests/xpcshell/test_listsources-02.js` (and `--task-ids --limit 0`)
- Expected: some hint that each of the 6 failing jobs had 470-1560 other TIMEOUTs (whole-job breakage).
- Got: "6x TIMEOUT Test exceeded time limit", a verdict of "intermittent" on one config. Only `fx-tests task <id>` per job revealed "1061 failing", "1564 failing", etc.
- Could show: per failing task, the job's total failing-test count, with a flag when it is far above normal ("mass failure: 1,154 other tests failed in this job").
- Workaround: ran `fx-tests task` on all 6 tasks.

### Question: "which bug are these failing jobs starred with?"
- Command: `fx-tests test <path> --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs annotated task LKys8j8iTQKTAN9hKv040g with.
- Got: no bug, because the bug summary does not name the test.
- Could show: bugs the failing tasks are annotated with, even when they name no test (`fx-tests intermittent --bug 1991833` already knows the task IDs).
- Workaround: Bugzilla quicksearch by hand.
