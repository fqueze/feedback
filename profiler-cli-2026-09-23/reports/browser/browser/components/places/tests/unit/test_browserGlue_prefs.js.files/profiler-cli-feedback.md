## Question: machine CPU % over a time range, in a resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (with and without a zoom)
- Expected: the CPU percent of each `CPU Use` marker in the row (it is the only interesting field), or a summary such as "CPU Use: mean 97%, min 53%, max 100% over the zoom".
- Got: rows with only the marker duration (e.g. `m-367 CPU Use t=1m44s 228ms`); the CPU value needs `marker info` per marker.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` per 5 s.
- What the output could show: the payload's `cpuPercent` inline in `--list` rows for CPU markers, or a counter-like "over time" section for `CPU Use`, as `counter info` does.
