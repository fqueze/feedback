## When did tests start and end, by status, over the job? (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0` gave 1389 rows.
- Expected: a timeline view, e.g. how many `test` markers started and ended in each 5 s bucket, grouped by status (the first word of the label).
- Got: a flat list, so I dumped `--json` and bucketed it with a script. That is how I found that 475 tests "started" in one 5 s window and all ended 45 s later.
- What would answer it: `--histogram <seconds>` combined with `--group-by` for interval markers.

## Machine CPU over time in a resource-usage profile
- Command: `profiler-cli profile info --session <s>` printed "CPU activity over time: No significant activity."; `counter list` printed "No counters".
- Expected: the machine's CPU over time, which is stored in `CPU Use` markers (cpuPercent field) in these profiles.
- Got: an output that reads as "idle", even though the `CPU Use` markers show 100% before the failure.
- Workaround: `--json` on `--search "name:CPU Use"`, then averaging `cpuPercent` per bucket in a script.
- What would answer it: `profile info` summarising `CPU Use` markers when a profile has no samples or counters.
