## Question: "what was the machine's CPU use in this 2 s window?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push 98.5,100.6`
- Expected: each `CPU Use` row showing its CPU % / idle % / iowait %, or a summary (min/avg/max CPU %) over the zoomed range.
- Got: rows with only name, time and duration (`m-56 CPU Use t=1m38s 99ms`). To see the numbers I had to run `marker info` on each marker, or use `--json` and a script over `fields`.
- Workaround: `--json` piped to python to print `cpuPercent`/`idle_pct`/`iowait_pct` for each marker.
