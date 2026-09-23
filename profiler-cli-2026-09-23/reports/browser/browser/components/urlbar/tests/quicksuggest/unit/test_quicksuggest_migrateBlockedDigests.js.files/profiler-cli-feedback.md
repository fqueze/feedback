## Question: how busy was the machine over time, in a resource-usage profile?
- Command: `profiler-cli profile info --session <s>` on an xpcshell `profile_resource-usage.json`.
- Expected: the machine's CPU use over time (the resource profile's main content).
- Got: "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile"; the CPU data only exists as 24k `CPU Use` interval markers.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script bucketing `cpuPercent` by 5 s / 30 s. A CPU-over-time summary (sparkline or buckets) for resource-usage profiles, respecting zoom, would have answered it.

## Question: how many tests were running concurrently at time t?
- Command: `thread markers --search "name:test" --list --limit 0 --json` + script counting overlapping `test` markers (excluding the `parallel`/`sequential`/`retry` phase markers).
- What could have shown it: a concurrency count in `marker info` for a `test` marker, or a `--group-by` over time.

## Question: which process type is each hanging child pid?
- Command: `thread markers --search "may be hanging at shutdown" --list --json` joined against `output` markers' `[GPU 8584, ...]` prefixes, by script.
- What could have shown it: nothing obvious in the tool; noting it as the question asked.
