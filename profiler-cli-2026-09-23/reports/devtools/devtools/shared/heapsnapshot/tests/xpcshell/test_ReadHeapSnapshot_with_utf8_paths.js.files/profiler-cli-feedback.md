## Question: "what was the machine's CPU use, per few seconds, over the 45 s this test was waiting?"

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --group-by name` (resource-usage profile of an xpcshell job, PyUxuOpdQj2b3T3XyQOKpg).
- Expected: some time-bucketed summary of the `cpuPercent` field, like `counter info`'s "over time" section.
- Got: only count and duration stats (`CPU Use: 1232 markers (avg=107.31ms, max=1.016s)`); the profile has no counters, so `counter info` does not apply.
- Workaround: `--list --limit 0 --json` piped into a Python script averaging `data.cpuPercent` weighted by duration per 5 s bucket.
- What could have shown it: a per-bucket summary of a numeric marker field (e.g. `--group-by time:5s --stat field:cpuPercent`), or exposing the resource-usage `CPU Use` markers as a counter.
