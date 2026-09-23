## Question: which of a test's failures are the same failure?

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marFailurePartialZucchiniBadAlloc.js --task-ids --limit 0`
- Question: how many of the 27 timeouts are the callback-log timeout, and how many are the earlier `NS_ERROR_FILE_ACCESS_DENIED` in setup?
- Got: all 27 are listed under one issue, `TIMEOUT Test exceeded time limit`. `fx-tests task <id>` also shows only `Test timed out` for each of them. The distinguishing lines (`JavaScript error … xpcshellUtilsAUS.js:3,416`, or the last `TEST-INFO` line before the timeout) are in the replayed log, but not in any summary.
- Workaround: loading all 27 resource-usage profiles and grepping each one for this test's log lines (about 15 minutes).
- What could have shown it: for a TIMEOUT, the last log line of the test before the timeout, plus any `JavaScript error` it logged, as a per-task column in `--task-ids`, or grouped under Issues.
