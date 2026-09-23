# fx-tests feedback (1990712)

## `task` misreports why a job was killed
- Command: `fx-tests task FijKq8dzSKy6l-KjfvuZTQ --profiles`
- Expected: the reason the job ended, as generic-worker logged it.
- Got: "task FijKq8dzSKy6l-KjfvuZTQ.0 was killed for exceeding its maximum duration". The log says otherwise: it ran 8m9s of a 30 min maxRunTime and ended with `task aborted due to sustained memory usage above 90% and available memory less than 500.00 MiB`.
- Workaround: grep the live_backing.log for `task aborted`.
- Also: the partial resource-usage profile it refuses to read is the only record of the machine's memory in these jobs; profiler-cli may still load it.

## Question: why did each job starred on a `[taskcluster:error]` bug end?
- Command: `fx-tests intermittent --bug 1990712 --since 45 --limit 0` (then `--json` for the task ids).
- Question: for a symptom-only bug (a generic-worker line printed after every kill), what actually ended each annotated job: max-run-time, the memory guard, a harness exit code, and the last log line before `Aborting task...`.
- Got: job names, platforms and task ids only; "Tests named" is empty because there is no TEST-UNEXPECTED line. Had to take the task ids from `--json`, download all 87 live_backing.log files (772 MB) and grep them.
- Could have shown: per occurrence, the generic-worker resolution line (`task aborted - max run time exceeded`, `task aborted due to sustained memory usage…`, `exit status N`) and the last `[task` line before it, grouped. That alone separates the modes of a tracking bug.
