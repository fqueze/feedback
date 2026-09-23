## Was the machine saturated during this window? (resource-usage profile)

- Command: `profiler-cli zoom push 409,427 --session S; profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0`
- Question: what was the machine's CPU use over the zoomed range (min/avg/max), and per minute over the whole job?
- Got: one row per 100-250 ms `CPU Use` marker with no value in the list row; the value is only in `marker info` or `--json`. `counter list` says "No counters in this profile".
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per bucket.
- Could show: a summary line (min/avg/max of cpuPercent) for CPU-type markers in `thread markers` aggregate output, or the value in the `--list` row.
