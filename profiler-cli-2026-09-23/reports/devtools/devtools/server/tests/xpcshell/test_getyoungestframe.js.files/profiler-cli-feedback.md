## Question: how busy was the machine over this time range? (resource-usage profile)

- Command: `profiler-cli zoom push 439,454 --session <s>`, then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json | python3 -c '<time-weighted mean of the cpuPercent field>'`.
- Expected: a summary of machine CPU over the zoom, e.g. from `profile info` or `counter list`. In the resource-usage profile, `counter list` says "No counters in this profile" and `profile info` says "No significant activity", even though the profile has a `CPU Use` marker every ~100 ms with `cpuPercent`.
- What its output could have shown: when the `CPU Use` markers exist, `profile info` (or `thread markers --search "name:CPU Use"` in aggregate mode) could print the mean, p10 and share of time at 95% or more for the current range. The brief says "Slow machine is shown only when the CPU tracks show it", so this is asked for every timeout and every slow-child failure.
