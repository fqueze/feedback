## Question: how busy was the machine's CPU over this time range?

- Command: `profiler-cli counter list --session <ru>` (on a resource-usage profile), then `profiler-cli thread markers --search "name:CPU Use" --session <ru>` over a zoomed range.
- Expected: some CPU-over-time summary for the range (average and max `cpuPercent`), the way `counter info` gives one for counters.
- Got: `No counters in this profile.`; the marker aggregate only gives the markers' durations (about 100 ms each), not their `cpuPercent` values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script that averages `data.cpuPercent` per second. The aggregate view could have shown min/avg/max of the numeric fields.
