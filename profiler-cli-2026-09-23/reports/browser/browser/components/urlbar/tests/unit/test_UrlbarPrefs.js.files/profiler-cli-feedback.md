## Question: "was the whole machine saturated during this window?" (resource-usage profile)

- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` (after `zoom push 1274,1295`), and `profiler-cli counter list` ("No counters in this profile").
- Expected: a summary of the machine's CPU use over the zoomed range (mean, share of time at 100%, per-second or per-bucket), like `counter info` gives for counters.
- Got: 168 individual `CPU Use` interval markers (several overlapping per second), each with a `cpuPercent` field; the aggregate view only gives marker counts and durations, not the field values.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` per second/minute, weighted by duration.
- What would have answered it: `thread markers --search "name:CPU Use"` aggregate showing min/mean/max of numeric payload fields, or exposing the resource-usage CPU markers as a counter track.
