## Question: "was the machine saturated between t=A and t=B?" (resource-usage profile)
- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list` after `zoom push 200,240`
- Expected: the CPU percent per marker in the list, or a summary (mean/max CPU over the view).
- Got: rows with only name/time/duration; the `cpuPercent` field needs `marker info` per row or `--json` plus a script. `profile info` on this profile says "CPU activity over time: No significant activity", which reads as "idle" although the CPU Use markers show ~99%.
- Workaround: `--json` and averaging `data.cpuPercent` per 2 s bucket in Python.
