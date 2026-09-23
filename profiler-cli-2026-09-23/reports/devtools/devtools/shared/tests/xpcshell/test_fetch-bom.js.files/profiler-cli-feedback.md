## Question: which tests were running at time T (resource-usage profile)?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a Python script keeping `test` markers whose `[start, start+duration]` contains T.
- Expected: a way to list the interval markers overlapping one instant, e.g. `thread markers --search name:test --at 44.918`. `zoom push 44.9,44.92` + `--list` does this in principle, but it prints every `test` marker since the start of the parallel phase along with SKIPs and the enclosing `parallel` marker, so it did not answer the question directly.
- Got: I had to write a script over the `--json` output.
- Workaround: the script above.

## Question: how were a job's test outcomes spread over time (when did tests stop passing, when did the TIMEOUTs start and end)?

- Command: the same `--json` dump plus a script bucketing `test` markers by status and by 5 s of start/end time.
- Expected: something like `thread markers --search name:test --group-by field:status` that also printed each group's first and last start and end times. The grouped output gives counts and durations, but not when each group happened.
- Got: counts only. This is what showed 1057 TIMEOUTs starting within 5 s with 20 threads, i.e. none of them had really run.
