## Question: how busy was the machine over time, in a resource-usage profile?

- Command: `profiler-cli profile info --session <s>` and `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0`
- Expected: a CPU-over-time summary (per-second or per-bucket CPU %) for an xpcshell resource-usage profile, the way `profile info` shows "CPU activity over time" for sampled profiles.
- Got: `profile info` says "CPU activity over time: No significant activity." (the profile has no samples, only `CPU Use` markers), and the `--list` rows for `CPU Use` markers show only time and duration, not the CPU Percent field.
- Workaround: `--json` and jq over `.flatMarkers[].data.cpuPercent`, averaged per second. The answer I needed ("machine at 100% from 38s to 52s, then ~0-2% until the job ends") could have been shown by `profile info` from the CPU Use markers, or by putting the CPU Percent in the list row.
