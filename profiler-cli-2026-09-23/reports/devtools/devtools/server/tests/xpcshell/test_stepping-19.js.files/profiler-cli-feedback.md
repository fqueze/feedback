## Question: what was the machine's CPU load over a time range (resource-usage profile)?

- Command: `profiler-cli zoom push 480,600 --session <s>; profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: some summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (duration-weighted average, min, or a per-N-seconds series), since the resource-usage profile has no counters (`counter list`: "No counters in this profile").
- Got: 969 individual markers, each only showing duration, the percentage visible only through `marker info`.
- Workaround: `--json` and a Python script computing a duration-weighted average per 5 s bucket from `data.cpuPercent`.
- What could have shown it: `thread markers --search "name:CPU Use"` aggregate stats could include min/avg/max of numeric payload fields, or the CPU Use markers could be exposed as a counter so `counter info` answers it.
