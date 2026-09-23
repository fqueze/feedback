## Machine CPU over one test's duration from a resource-usage profile
- Question: "was the machine saturated while this test ran?"
- Command: `profiler-cli zoom push m-59 --session S` (the test marker), then `profiler-cli thread markers --session S --search 'name:CPU Use' --list --limit 0`
- Expected: the CPU percent on each row, or a summary (min/avg/max) of the CPU Use markers in the zoomed range.
- Got: 113 rows with only name, time, and duration. The CPU percent is only in `marker info` for each marker.
- Workaround: `--json`, then a Python script bucketing `data.cpuPercent` per second.
