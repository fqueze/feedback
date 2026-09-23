## Host CPU over a test's time range needs a script
- Command: `profiler-cli zoom push m-1` (a `test` marker), then `profiler-cli thread markers --search "CPU Use" --list --limit 0`
- Expected: the CPU percentage of each sample, or a summary of it (mean, min and max per second) over the zoomed range.
- Got: 477 rows showing only name, time and duration (`CPU Use  t=29m27s  97ms`). The `cpuPercent` value is only in `--json` `fields`.
- Workaround: `--json`, then a Python script to bucket `data.cpuPercent` per 100 ms and per second, relative to the test marker's start.
- Question it could have answered: "was the machine saturated during this test, and at what moment?"
