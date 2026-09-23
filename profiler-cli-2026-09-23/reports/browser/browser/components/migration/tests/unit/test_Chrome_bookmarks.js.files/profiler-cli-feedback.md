## Was the machine saturated while this test ran?

- Command: `profiler-cli zoom push m-111 --session <s>; profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: a summary of the resource-usage `CPU Use` markers over the zoomed range (mean / max CPU %, share of time above 90%), as `counter info` gives for counters.
- Got: 408 individual rows with no CPU value in the text output (the percentage is only in `--json` `data.cpuPercent`); the aggregate view gives only marker counts and durations.
- Workaround: `--json` piped to a Python script averaging `data.cpuPercent` weighted by duration.
