## Question: "were this test's failures part of a job-wide collapse?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_dbgglobal.js` (and `--bugs`, `--task-ids --limit 0`)
- Expected: some hint that all 6 failures came from jobs where hundreds of other tests failed the same way (477 to 1,564 failing tests per job), and the job-level bug sheriffs starred them on (bug 1991833, "High frequency win-msix PermissionError: [WinError 5] Access is denied").
- Got: "Verdict: intermittent. Fails on 1 configuration" and "Issues: 6x TIMEOUT Test exceeded time limit"; `--bugs` found nothing. It reads like a test-specific intermittent.
- Workaround: ran `fx-tests task <taskId>` on each of the 6 tasks to see the "N tests, M failing" header, then `fx-tests intermittent --bug 1991833` once I had found the bug from the traceback.
- What could have shown it: per failing task, the job's failing-test count (or a flag like "job had >100 failures"), and the bugs the job was annotated with, in `--task-ids` output.
