## `zoom push` with the minute-format times profiler-cli prints silently zooms to the wrong range

- Command: `profiler-cli zoom push 9m20s,9m40s --session <s>` (the resource-usage profile prints marker times as `t=9m34s`)
- Expected: a zoom to 560–580 s, or an error saying the format is not accepted.
- Got: `Zoom depth: 1` with view `ts-1→ts-2 (1.000ns)` near t=9s. The next `thread markers --list` showed only the markers around 9 s, with no warning.
- Workaround: convert to seconds by hand (`zoom push 560,580`).

## Question: "how busy was the machine between t1 and t2?" in a resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` piped through a Python script, to average `data.cpuPercent` per 5 s bucket.
- Expected: some default view that answers "was the machine saturated when this timeout hit", e.g. a CPU-use summary over the zoomed range from `thread markers --search "CPU Use"`, or `counter info`. The resource-usage profile has no counters (`counter list`: "No counters in this profile"). The marker aggregate prints only counts and durations, not the `cpuPercent` field values.
- Could have shown: min/avg/max of the numeric payload fields of the matched markers, over the current zoom, perhaps bucketed over time like `counter info`'s "over time" section.
