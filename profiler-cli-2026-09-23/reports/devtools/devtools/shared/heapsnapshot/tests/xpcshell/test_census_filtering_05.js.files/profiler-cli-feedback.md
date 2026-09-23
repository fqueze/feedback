## Question: "was the machine CPU-saturated during window X?" (resource-usage profiles)
- Command: `profiler-cli counter list --session <s>` gave `No counters in this profile`. Machine CPU in these profiles is only in `CPU Use` interval markers (`cpuPercent` field).
- Workaround: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, plus a Python script averaging `data.cpuPercent` per 1 s / 5 s bucket.
- What would have answered it: in the aggregate `thread markers` view, the mean/min/max of numeric fields for one marker name within the current zoom, or exposing these markers as a counter.
