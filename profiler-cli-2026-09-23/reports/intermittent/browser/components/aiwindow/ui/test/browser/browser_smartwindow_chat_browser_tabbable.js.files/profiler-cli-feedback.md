## Question: was the machine CPU-saturated between t1 and t2 (resource-usage profile)?

- Command: `profiler-cli thread markers --session S --search 'name:CPU Use' --list --limit 0`, zoomed on 1939,1960.
- Expected: the CPU percentage per marker, or a summary for the range (mean/max cpuPercent).
- Got: each `CPU Use` row shows only its name and duration. The `cpuPercent` field only shows up in `--json`, and `counter list` says "No counters in this profile".
- Workaround: parsed `--json` in a script to average `cpuPercent` for each second.
