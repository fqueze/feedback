## Question: "was the machine busy or idle during this time range?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json` then a python script averaging `data.cpuPercent` over time windows.
- Expected: a way to get the mean/max machine CPU over a zoomed range from a resource-usage profile (the aggregate view of `thread markers` gives interval durations for `CPU Use`, not the CPU values), e.g. field stats in the aggregate view or `counter info`.
- Got: `counter list` says "No counters in this profile"; the aggregate `thread markers` output only lists counts and durations of `CPU Use` markers.
- Workaround: JSON dump + script (`cpu-1.json` in this directory).
