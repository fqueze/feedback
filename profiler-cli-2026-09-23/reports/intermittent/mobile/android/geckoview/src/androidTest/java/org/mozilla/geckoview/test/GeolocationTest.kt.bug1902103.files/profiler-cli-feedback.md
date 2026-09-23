## Question: what was the machine's CPU use, second by second, during a test's wait? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0` (zoomed to the test)
- Expected: the CPU percentage of each `CPU Use` marker, or a per-bucket summary of it.
- Got: only handle, time and duration per row; the value needs `marker info` one marker at a time. `profile info` on the same profile says "CPU activity over time: No significant activity" although the `CPU Use` markers read up to 93%.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per second.
- What the default output could show: the payload's main field (`cpuPercent`) in the `--list` row, or a `counter`-like over-time view for `CPU Use` markers.
