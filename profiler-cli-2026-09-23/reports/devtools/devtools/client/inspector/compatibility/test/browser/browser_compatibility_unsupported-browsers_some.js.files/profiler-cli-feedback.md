## Question: what was the machine's CPU use over a time range (resource-usage profile)?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 530,541`) on a `profile_resource-usage.json`.
- Expected: each row showing its `CPU Percent` (or a min/avg/max summary for the range), so "was the machine saturated when X happened" is one command.
- Got: 110 rows of `CPU Use  t=8m57s  100ms  ✗` with no value; the percentage is only in `marker info` (one marker at a time), and `counter list` says "No counters in this profile".
- Workaround: `--json` and a Python script over `flatMarkers[].data.cpuPercent` (a string like `'48.0%'`).
- What the output could have shown: the CPU Percent in the list row's description column, or a range summary (min/avg/max, samples above 95%).
