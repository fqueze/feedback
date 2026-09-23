## Resource-usage profile: machine CPU is only in `CPU Use` markers

- Command: `profiler-cli counter list --session <s>` and `profiler-cli profile info --session <s>` on `.../public/test_info/profile_resource-usage.json` (task Xxr5rCPkSwCY8T4P8nONtQ).
- Expected: the machine's CPU use over time, as the brief calls it "the CPU use of the whole machine".
- Got: "No counters in this profile." and "CPU activity over time: No significant activity." The data is in 18,800 `CPU Use` markers (`cpuPercent` field), 10 per second.
- Workaround: `thread markers --search "name:CPU Use" --list --json` in a zoom, bucketed per second with a script. The question was "was the machine saturated at t=537 s"; `profile info` could have summarised the `CPU Use` markers as it does CPU activity.
