## Question: what was the machine's CPU use while one test ran?
- Commands: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on a resource-usage profile.
- Expected: the CPU percent of each `CPU Use` marker in the list, or a summary (mean/min/max) of the field over the zoom.
- Got: rows with only name, time and duration; the CPU Percent needs `marker info` per handle. For a job-wide baseline I had to list 9,175 handles with `--json`, pass them all to `marker info --json`, and average `cpuPercent` with a script.
- What would have answered it: CPU Percent in the `--list` row for CPU markers, or a field-aggregation option (e.g. `--stats-field cpuPercent`).
