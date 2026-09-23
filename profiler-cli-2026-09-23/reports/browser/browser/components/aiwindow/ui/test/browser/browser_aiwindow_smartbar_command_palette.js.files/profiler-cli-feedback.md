## Question: how busy was the machine during this time range?

- Command: `profiler-cli zoom push 501,800` then `profiler-cli thread markers` on a resource-usage profile (no counters; CPU is in `CPU Use` markers).
- Expected: the average (and maximum) `CPU Percent` over the zoomed range, to tell "blocked, machine idle" from "machine saturated" during a 5-minute stall.
- Got: a count of 2991 `CPU Use` markers with their interval durations; the CPU values are only in each marker's payload.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script averaging `data.cpuPercent` per time bucket.
- Suggestion: for markers with numeric payload fields (CPU Use, Memory), have the aggregate view print min/avg/max of those fields in the current range. Or have `profile info`'s "CPU activity over time" use them: it said "No significant activity" for this profile.
