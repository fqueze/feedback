## Question: "how busy was the machine over time" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: a CPU% timeline, or at least the CPU Percent value on each row.
- Got: rows carry only name/time/duration; the CPU Percent is only in `marker info` or `--json`. `counter list` says "No counters in this profile".
- Workaround: `--json` plus a Python script bucketing `data.cpuPercent` per 5 s. The default output could show the key field of CPU markers inline, or `profile info` could draw a CPU sparkline from these markers.

## Bare negative term in `thread markers --search` silently matches nothing
- Command: `profiler-cli thread markers --search "-name:test,-name:CPU Use,-will retry" --list`
- Expected: `-will retry` excludes markers containing "will retry" (like a bare positive term matches everywhere).
- Got: "No markers match the specified filters." with no warning that the bare exclusion was not understood / excluded everything.
- Workaround: dropped the bare exclusion, dumped to a file and grep -v'd.
