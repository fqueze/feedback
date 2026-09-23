## No way to summarize a payload field of periodic markers over a range
- Question: "was the machine busy while the test hung?" (resource-usage profile, `CPU Use` markers, 8m28s-14m35s)
- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0 --json` after `zoom push 510,870`, then a Python script to average `cpuPercent` per 30 s bucket.
- Expected: something like `thread markers --search 'name:CPU Use' --stats cpuPercent` (min/avg/max, or per-bucket like `counter info`'s "over time" section).
- Got: only per-marker rows (3,600 of them) or aggregate durations, not payload values. The profile has no counters, so `counter info` does not apply.
