## Question: how busy was the machine during this time range (resource-usage profile)?

- Command: `profiler-cli thread markers --session 1851682-1 --search "name:CPU Use" --list --limit 0` (and `profiler-cli counter list`, which says "No counters in this profile"; `profile info` says "CPU activity over time: No significant activity.")
- Expected: CPU use of the machine over time, or averaged over a zoomed range, as for a counter.
- Got: 17,493 individual 100 ms `CPU Use` markers, one row each, with the percentage only in `marker info`.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` per minute.
- What would have helped: `counter info`-style "over time" buckets for the resource-usage `CPU Use` / `Memory` markers, or `thread markers --search "name:CPU Use"` printing min/avg/max of the percentage in the aggregate view.
