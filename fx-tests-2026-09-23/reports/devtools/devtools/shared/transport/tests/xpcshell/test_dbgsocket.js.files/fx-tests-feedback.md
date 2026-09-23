## Question: is this test's failure its own, or one symptom of a job-wide breakage?

- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_dbgsocket.js --task-ids --limit 0`
- Expected: next to the one failing task, a hint that the job itself was broken. Here 1,375 of 1,854 tests timed out in that job (XfKT04wZR42kSNwV6wuRpQ.0), which settles the diagnosis before any profile is opened.
- Got: `1x TIMEOUT Test exceeded time limit` and the task id, with nothing about the job. `--bugs` found no bug either, because the job-wide bug (1991833, "win-msix PermissionError: [WinError 5] Access is denied") never names the test.
- Workaround: `fx-tests task <id> --profiles`, which does print "1376 failing, 1375 TIMEOUT".
- Could show: per failing task, the job's failing/total test count, or a "job-wide: N% of tests failed" flag.

