# profiler-cli feedback (review of MediaSessionTest.kt.bug2051635)

## Question: "how busy was the machine over this range?" (resource-usage profile)
- Command: `profiler-cli zoom push 517.5,524.2` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on CrreROmoRyCHMJXYNLbjKQ run 1's `profile_resource-usage.json`.
- Expected: each CPU Use marker with its `cpuPercent`, or a min/avg/max over the view.
- Got: 68 rows of `CPU Use  t=8m38s  100ms` with no value. `profile info` and `thread info` also say "No significant activity" and `counter list` says "No counters", on a profile whose only CPU data is these markers.
- Workaround: `marker info m-67..m-134 --json` and a Python script to pull `cpuPercent`.
- Could have shown: the marker's main field (`cpuPercent`) in the list row, and CPU Use markers counted as CPU activity in `profile info`.
