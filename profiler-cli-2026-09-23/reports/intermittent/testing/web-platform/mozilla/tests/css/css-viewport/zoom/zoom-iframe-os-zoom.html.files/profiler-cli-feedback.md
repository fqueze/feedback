## Question: how busy was the machine during one test (resource-usage profile)?

- Command: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: a CPU-over-time summary for the zoomed range (like `counter info`'s "over time" buckets), so "was the machine saturated or idle when the timeout hit" is one command.
- Got: `profile info` says "No significant activity" and `counter list` says "No counters" for a mozharness resource-usage profile; the CPU data is only in 328 `CPU Use` markers, one per 100 ms, each needing `marker info` to read its percentage.
- Workaround: `--json` on the marker list and a python script bucketing `data.cpuPercent` per 2 s.
