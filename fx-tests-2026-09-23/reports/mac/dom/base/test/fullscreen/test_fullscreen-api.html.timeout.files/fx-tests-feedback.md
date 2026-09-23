## Where each execution of a timed-out test stopped

- Question: "for each failing execution on this try push, what was its first failure, and which subtest was it in when it timed out?" (16 executions, 13 timeouts, hanging in 5 different subtests for 3 different reasons).
- Commands: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --task-ids --messages`, `--json`, `fx-tests task ajIfcoMXQIK0m0cfD5JKyw --profiles`.
- Got: message counts summed over the push (the `Test timed out` row has no count), and per task only `TIMEOUT — 2 failing executions of 2`. Nothing per execution: not which message came first, nor the last test log lines before the timeout.
- Workaround: parsed the `live_backing.log` of each task (downloaded by the sibling report) with a script, splitting at each `TEST-START` of the test and printing the first `TEST-FAIL`/`TEST-UNEXPECTED-*` and the lines before `Test timed out`. Also: first runs log `TEST-FAIL`/`TEST-TIMEOUT`, retries `TEST-UNEXPECTED-*`, so a grep on `UNEXPECTED` alone misses every first run.
- What would have answered it: `fx-tests task <id> --executions` (or `try --executions`) listing per execution its first failure and, for a timeout, the last few buffered log lines.
