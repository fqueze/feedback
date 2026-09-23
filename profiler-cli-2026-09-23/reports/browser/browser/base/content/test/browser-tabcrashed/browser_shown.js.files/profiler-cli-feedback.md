Question: machine CPU over a zoom range (mean/min idle of CPU Use markers). Command: profiler-cli thread markers --search 'name:CPU Use' --list --json | python mean. Default output only aggregates durations, not the CPU percent fields.

## Machine CPU over a zoomed range of a resource-usage profile
- Question: was the machine busy or idle during a 6-minute hang (zoom 70,430)?
- Command: `profiler-cli counter list` (says "No counters in this profile"), then `profiler-cli thread markers --search "name:CPU Use"` (aggregates only durations).
- Expected: a mean/min/max of the `CPU Use` markers' cpuPercent / idle_pct over the view, or a CPU counter.
- Got: only marker counts and durations; had to script over `--list --json` to average `idle_pct`.
- Workaround: `--list --limit 0 --json` piped to python.
