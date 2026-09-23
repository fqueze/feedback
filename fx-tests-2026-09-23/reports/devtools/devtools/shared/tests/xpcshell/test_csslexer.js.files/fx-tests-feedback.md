## Question: "which bug were this test's failing jobs starred on?"

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_csslexer.js --bugs`
- Expected: bug 1991833, which all 3 failing jobs (JOcVulCf, FADMjPT5QXm, ZtblArCL) were starred on by sheriffs.
- Got: no bug section at all; `--bugs` only finds bugs whose summary names the test, and 1991833's summary ("High frequency win-msix PermissionError: [WinError 5] Access is denied") names none. `fx-tests intermittent --bug 1991833` covers only the last 7 days, so it cannot show these jobs (08-31 to 09-07) either.
- Workaround: Treeherder API, `jobs/?task_id=<id>` then `bug-job-map/?job_id=<id>`. What the output could have shown: for each failing task in `--task-ids`, the bug it was starred on.
