## Question: exactly when did each marker of the test happen (to the millisecond)?

- Command: `profiler-cli thread markers --session <s> --search test_stepping-18 --list --limit 0`
- Expected: timestamps precise enough to measure a 1 to 10 s gap between markers.
- Got: `t=7m58s` for everything in a 27-minute profile, so 20 markers across 2.5 s all read `7m58s`. `marker info` also printed `Time: 8m10s`.
- Workaround: `--json` and `start/1000` in a script. A millisecond (or 3-decimal seconds) column in `--list` would answer it.

## Question: how busy was the machine during this interval, and over the job?

- Command: `profiler-cli zoom push 472,492` then `thread markers --search "name:CPU Use" --list --json`, averaged in Python (and per minute over the whole job).
- What could show it: resource-usage profiles have no counters (`counter list` says "No counters"), only `CPU Use` markers. A summary of the `CPU Use` markers' `cpuPercent` (mean/min/max in the current zoom, and a per-minute sparkline) would answer "was the machine saturated when X happened" directly.

## Question: how are these markers distributed over time?

- Command: `thread markers --search "may be hanging at shutdown" --list --json`, bucketed per minute in Python.
- What could show it: a histogram or per-bucket count in the aggregate (non-`--list`) view, like `counter info`'s "over time" section.
