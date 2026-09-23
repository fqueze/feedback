## Question: what was the machine's CPU use over time, in a resource-usage profile?

- Command: `profiler-cli profile info --session browser-test_suggestBackendMl.js-1` on a job's `profile_resource-usage.json`
- Expected: the CPU-over-time section to reflect the machine's CPU.
- Got: "No significant activity", because the CPU is in `CPU Use` interval markers (cpuPercent / system_pct fields), not samples or counters; `thread markers --search "CPU Use"` only aggregates durations.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script bucketing cpuPercent and system_pct per minute.
- Could show: a per-bucket summary of CPU Use (total and system) in `profile info` for resource-usage profiles, respecting zoom.

## Question: which tests ran at the same time as mine, and how did they end?

- Command: `thread markers --search "name:test" --list` in a resource-usage profile
- Needed: the tests overlapping a time window, with status and the time from their start to their "exiting test" line.
- Got: one row per test in chronological order over the whole job (2,119 markers); overlap with a window needs `--json` and a script.
- Could show: a `zoom push` that keeps interval markers overlapping the range (not only those starting in it), or a `--overlapping` flag.
