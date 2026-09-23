## Question: "how busy was the machine during this test compared with before it?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a Python script to average `cpuPercent` over 30 s windows.
- Why: `counter list` says "No counters in this profile". Machine CPU exists only as 23,000 `CPU Use` interval markers. The default marker summary gives durations but not the payload values, so there was no way to get the mean CPU inside a zoom range.
- What would have answered it: a numeric field summary for a marker type over the current zoom (for example `thread markers --search "name:CPU Use" --field-stats cpuPercent` giving min/mean/max), or exposing the resource-usage CPU markers as a counter so `counter info` works under `zoom push`.
