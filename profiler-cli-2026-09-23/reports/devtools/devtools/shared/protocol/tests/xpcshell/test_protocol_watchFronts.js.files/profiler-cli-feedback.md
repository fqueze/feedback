## Question: how busy was the machine during the test's 45 s window (resource-usage profile)?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 5 --session <s>` (zoomed to 53.2,98.4)
- Expected: each row showing its `CPU Percent`, or the aggregate view giving min/avg/max of `cpuPercent` over the zoom.
- Got: rows with only name, time and duration; the aggregate gives duration stats only. The value needs `marker info` per marker.
- Workaround: `--list --limit 0 --json` then a Python script bucketing `data.cpuPercent` per 5 s.
- What would have answered it: the CPU marker's main field in `--list` rows, and a per-field numeric summary (or time-bucketed average) in the aggregate for CPU/Memory markers.
