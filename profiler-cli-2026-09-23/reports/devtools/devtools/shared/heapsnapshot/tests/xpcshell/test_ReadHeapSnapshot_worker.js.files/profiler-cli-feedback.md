## Question: what was the machine's CPU use over time, in a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percentage per marker (or a per-bucket summary), since that is the only CPU data in a resource-usage profile (`profile info` says "No significant activity" and `counter list` says "No counters").
- Got: one row per marker with its time and duration only; the `cpuPercent` field is only visible with `marker info` or `--json`.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` per 5 s. A `CPU Use` label showing the percent in `--list`, or a CPU track in `profile info` built from these markers, would answer it directly.
