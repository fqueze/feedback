## Question: is this test's failure its own, or one of hundreds in a job-wide meltdown?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-15.js` (and `--task-ids --limit 0`)
- Expected: some hint that every one of the 6 failing jobs had 476-1563 other TIMEOUTs (i.e. the job broke, not the test).
- Got: a clean "intermittent, 1.8% on msix, TIMEOUT 6x" verdict that reads like a test problem. I had to run `fx-tests task <id>` on each of the 6 tasks to see "1061 failing / 1564 failing / ...".
- Could have shown: per failing run, the number of other failures in the same job (e.g. "6 of 6 failures were in jobs with >400 failing tests"), which answers the question in one line.

## Question: why did this job start failing everything, and when?

- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles --limit 0`
- Expected: the harness-level error that explains the meltdown.
- Got: 477 per-test rows; the one harness-level `PermissionError: [WinError 5] Access is denied` traceback (logged without a test name, after "Following exceptions were raised:") is not shown anywhere. Found it only by loading the resource profile.
- Workaround: profiler-cli on the resource profile, then a script over `thread markers --json` (onset.py in this directory) to find the first test that "failed" within ms of starting and the tests running then.
- Could have shown: a "Harness errors" section with ERROR lines that carry no test name, and the time the failure cascade began (first of N consecutive failures) with what was running.
