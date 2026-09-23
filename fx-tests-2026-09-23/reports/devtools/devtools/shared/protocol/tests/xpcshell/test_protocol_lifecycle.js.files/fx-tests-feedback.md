## `fx-tests task` hides a job-wide harness breakdown behind 1152 per-test "Test timed out" rows

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: some sign that the job itself broke. Here 1152 of ~1200 launched tests TIMEOUT, and all of them started within 10 s of each other after t=50s. The harness's own `PermissionError: [WinError 5] Access is denied` traceback from `launchProcess` (an ERROR marker at the end of the run) is the actual cause.
- Got: a 1154-row FAILED list, each row reading "Test timed out" / "force-killed by the harness". Nothing points at the traceback, and nothing marks the point in the timeline where everything started failing.
- Workaround: `profiler-cli ... --search name:test --list --limit 0 --json` plus a script bucketing statuses by start time, and guessing `--search Traceback` to find the harness exception.
- Question the default output could answer: "did this job break at some point, and why?". For example: "no test passed after t=49.1s; 1148 tests started after it and all timed out", plus any harness-level ERROR/Traceback that is not attached to a test.
