## profiler-cli: resource-usage CPU load not readable without --json

- Question: "was the machine saturated while this test ran?"
- Commands: `profiler-cli profile info` ("CPU activity over time: No significant activity"), `profiler-cli counter list` ("No counters in this profile"), then `zoom push m-1` + `thread markers --search "name:CPU Use" --list`.
- Expected: the CPU percentage per interval, or a summary (max/mean) over the zoomed range.
- Got: one row per `CPU Use` marker with only name, time (to the second) and duration; the `cpuPercent` field is only in `--json`.
- Workaround: `--json` piped into a python one-liner for max/mean of `data.cpuPercent`.
