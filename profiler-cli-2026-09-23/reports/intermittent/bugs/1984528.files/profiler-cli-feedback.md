# profiler-cli feedback (bug 1984528)

## Question: was the machine busy or idle during this range of a resource-usage profile?

- Commands: `profiler-cli zoom push 1646,1912 --session 1984528-1`, then `profiler-cli thread markers --session 1984528-1 --search "name:CPU Use" --list --limit 5`
- Expected: the CPU Percent values over the range: avg, min and max, or a per-bucket series. On a resource-usage profile, that is the main question.
- Got: the list shows only marker durations (94ms, 110ms, ...). The aggregate view shows duration stats. `counter list` says "No counters in this profile", and `profile info` says "CPU activity over time: No significant activity."
- Workaround: `--list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` per 15 s and 30 s.
- It could show: field stats for numeric payload fields (`cpuPercent`, `Memory used`) in the aggregated marker view. Or it could expose the resource-usage CPU and memory markers as counters, so that `counter info` works under zoom.
