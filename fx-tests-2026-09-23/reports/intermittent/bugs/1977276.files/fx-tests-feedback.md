## `task` refuses the partial resource-usage stream of an aborted task, and says the wrong reason

- Command: `fx-tests task JG7vqs8CRAiuci2fL5JCUA --profiles`
- Expected: whatever the partial stream holds (the Memory and CPU tracks, the phases), for a task taskcluster aborted with "task aborted due to sustained memory usage above 90% and available memory less than 500.00 MiB".
- Got: "task JG7vqs8CRAiuci2fL5JCUA.0 was killed for exceeding its maximum duration, so its profile is a partial stream ... Its duration is the problem to look at". The task ran 8m24s of a much longer max-run-time; it was killed for memory, not duration. The message sends the reader after the wrong cause.
- Workaround: downloaded the JSONL artifact and parsed the Memory markers with a script.
- Question the tool could answer: "how much memory was used over time in this job, and which phase/test was running when it peaked".

## Question: "how much system memory did this job use over time, and when did it peak"

- Asked of a finished resource-usage profile (task DsFt3prsRL6AHs4C1MUlNg, talos-bcv) to compare with the jobs killed for memory.
- Command: `profiler-cli thread markers --search name:Memory --list --limit 0 --json --session 1977276-1`, then a python script over `flatMarkers[].data.used`.
- `fx-tests task --profiles` could have shown it: a peak and a start-to-end slope of the Memory track, next to the CPU summary. profiler-cli has no counter for it (`counter list`: "No counters in this profile"), since the resource monitor stores memory as markers.
