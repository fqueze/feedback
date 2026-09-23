## What was the machine's CPU use during one test's run?

- Command: `profiler-cli zoom push 155.0,164.0 --session <s>; profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0 --json` on a resource-usage profile, then a python script to duration-weight `data.cpuPercent`, `user_pct`, `system_pct`.
- Expected: the default (non-JSON) marker output for `CPU Use` in a zoomed range to show the average/min/max CPU percent, as `counter info` does for counters.
- Got: the list shows only durations; the percentages are in each marker's fields, one `marker info` per marker.
- Workaround: script over `--json` (`cpuavg.py` in this directory).
