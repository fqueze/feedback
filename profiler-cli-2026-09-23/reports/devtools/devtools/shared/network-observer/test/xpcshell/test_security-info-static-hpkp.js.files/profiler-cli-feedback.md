## Question: how busy was the machine while this test ran?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --session … ` (resource-usage profile, zoomed to the test's interval)
- Expected: each `CPU Use` row to show its cpuPercent / idle value.
- Got: rows with only a name and a duration (`CPU Use  t=2m30s  99ms`). The values are only in `--json` `fields`.
- Workaround: a Python script over `--json` to print cpuPercent/idle_pct per marker.
- Also: `profile info` reports "No significant activity" and `counter list` reports "No counters", so neither answers it for a resource-usage profile.
