## Question: how busy was the whole machine, minute by minute, around a failure (resource-usage profile)?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` weighted by duration into 30 s bins.
- Expected: a built-in view of the machine CPU track over time (the resource-usage profile has no counters: `counter list` says "No counters in this profile"), e.g. `thread markers --search "CPU Use" --over-time 30s`, or `profile info`'s "CPU activity over time" picking up the CPU Use markers.
- Got: `profile info` says "No significant activity" for the only thread; 23,975 CPU Use markers only readable one by one.
- Workaround: the script above.
