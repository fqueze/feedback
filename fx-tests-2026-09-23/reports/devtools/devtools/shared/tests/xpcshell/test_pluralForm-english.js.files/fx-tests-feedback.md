## Which bug were this test's failing jobs starred on?

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_pluralForm-english.js --bugs`
- Expected: the bug the sheriffs starred the 3 failing jobs with (bug 1991833, "High frequency win-msix PermissionError: [WinError 5] Access is denied").
- Got: no bug section at all; `--bugs` only finds bugs whose summary names the test, and this one names none.
- Workaround: Treeherder REST by hand, `jobs/?task_id=<id>&retry_id=0` for the job id, then `bug-job-map/?job_id=<id>`, once per failing task.
- What the output could show: for each failing task in `--task-ids`, the bug(s) it was classified with (the bug-job-map), since those tasks are already listed.
