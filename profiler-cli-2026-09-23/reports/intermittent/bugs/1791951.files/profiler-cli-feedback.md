## Question: "what was the machine's CPU / IO-wait use over this time range?" on a resource-usage profile

- Command: `profiler-cli profile info --session 1791951-1` on `CIaXD8eATM6V9vhX-NjSGg/.../profile_resource-usage.json`, then `profiler-cli counter list`.
- Expected: machine CPU use over time (the brief calls it "the CPU use of the whole machine").
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is in 16968 `CPU Use` interval markers (fields cpuPercent, iowait_pct, idle_pct), which `profile info` does not summarize.
- Workaround: `zoom push 40,240` + `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `cpuPercent` / `iowait_pct` per 10 s / per minute bucket. It showed 20-46 % IO wait and <7 % CPU for the first 7 minutes, then <1 % IO wait and ~30 % CPU.
- What could have shown it: `profile info` (or `counter list`) summarizing `CPU Use` markers of a resource-usage profile as a time series, including IO wait, the way it does for sampled CPU.
