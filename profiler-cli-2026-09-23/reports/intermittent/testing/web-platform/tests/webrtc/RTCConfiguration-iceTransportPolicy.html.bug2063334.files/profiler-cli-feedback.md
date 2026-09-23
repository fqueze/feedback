## Question: was the machine busy during this test (resource-usage profile)
- Command: `profiler-cli thread markers --session S --search "CPU Use"` after `zoom push m-1`
- Expected: a summary of the CPU percentage over the zoomed range (mean/max of `cpuPercent`).
- Got: only count and interval durations of the `CPU Use` markers; the value is per-marker in `marker info`.
- Workaround: `--list --limit 0 --json` and a Python script over `fields[].cpuPercent` (mean 8.9 %, max 98.1 %). A numeric-field aggregate (mean/max per field) in the default marker summary would answer it.
