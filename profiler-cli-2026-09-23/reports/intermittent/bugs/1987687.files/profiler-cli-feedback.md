## Machine CPU over a time range in a resource-usage profile

- Question: was the host busier during the failing launch than during a passing launch in the same job?
- Command: `profiler-cli zoom push 125,137 --session 1987687-1; profiler-cli thread markers --session 1987687-1 --search "name:CPU Use" --list --limit 0 --json`
  then a Python script to average `cpuPercent` per second.
- Expected: a summary of CPU% over the zoomed range (min/avg/max, or per-bucket like `counter info`'s "over time").
- Got: `counter list` says "No counters in this profile". CPU is only available as 120 per-100 ms `CPU Use` markers. The default text output lists them one per row with no values aggregated.
- Could show: `thread markers --search "name:CPU Use"` in aggregate mode could print field stats (avg/max of cpuPercent) for numeric payload fields.
