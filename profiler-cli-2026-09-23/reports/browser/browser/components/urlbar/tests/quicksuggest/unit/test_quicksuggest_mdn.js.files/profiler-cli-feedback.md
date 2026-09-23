## Question: what was the machine's CPU use over this time range?

- Command: `profiler-cli zoom push --session S 1414,1460` then `profiler-cli thread markers --session S --search "CPU Use" --list --limit 0`
- Expected: the CPU percentage of each `CPU Use` marker in the list row, or an aggregate (duration-weighted average, min, max) for the zoomed range.
- Got: 359 rows of `CPU Use  t=23m34s  111ms` with no value; the percentage is only in `marker info` or `--json` `fields[0]`.
- Workaround: `--json` and a Python script computing a duration-weighted average of `cpuPercent`.
- What could have shown it: the `CPU Percent` field in the list row, or a summary line for `CPU` markers in the aggregated (non `--list`) view.

## Question: which xpcshell process (pid) ran each test, and when did each start?

- Command: `profiler-cli zoom push --session S 280,345` then `profiler-cli thread markers --session S --search cppDebug --list --limit 0`
- Expected: a way to see the process ID of each resource-usage `cppDebug` marker (and so the first time each pid appears), to place a child pid such as the GPU process's among the parents' pids.
- Got: the list rows show only the message; `Process ID` and `Test` are only in `marker info` or `--json` `fields`.
- Workaround: `--json` and a Python script collecting the first marker per `Process ID`.
- What could have shown it: `--group-by field:<Process ID key>` with first/last timestamp per group, or the pid in the list row.
