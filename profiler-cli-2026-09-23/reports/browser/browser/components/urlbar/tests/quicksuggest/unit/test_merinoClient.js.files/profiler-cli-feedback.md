## Question: "was the machine CPU-saturated at time T?" on a resource-usage profile
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a python script averaging `data.cpuPercent` per 10 s bucket.
- Expected: `profile info` "CPU activity over time", or `counter list`, to show the machine CPU of a resource-usage profile.
- Got: `profile info` says "No significant activity" and `counter list` says "No counters in this profile"; the CPU is only in 17,119 `CPU Use` markers. A marker-field time series (e.g. `thread markers --search "name:CPU Use" --over-time cpuPercent`) or treating those markers as a counter would have answered directly.
