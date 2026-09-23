## Question: what was the machine's CPU use over a time window?
- Command: `profiler-cli zoom push 340,365 --session test_restartFrame-01.js-1` then `profiler-cli thread markers --session test_restartFrame-01.js-1 --search "CPU Use" --list --limit 0 --json | python3 …` to average `data.cpuPercent` per 5 s bucket.
- Expected: a summary of the `CPU Use` markers in the zoomed range (mean/min/max, or per-bucket) from `thread markers --search "CPU Use"` without `--list`, or from `counter info`.
- Got: the aggregated view gives counts/durations only, not the payload values; the `--list` output is ~40 rows per 5 s.
- Workaround: script over `--json`.
