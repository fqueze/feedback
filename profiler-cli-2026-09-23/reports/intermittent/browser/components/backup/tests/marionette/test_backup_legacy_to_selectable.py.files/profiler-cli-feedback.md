## Question: "what was the machine's CPU use during this 1 s window?" (resource-usage profile; same as the sibling report)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push 46.6,48.1`
- Expected: the CPU % / idle % / iowait % of each row, or a min/avg/max over the zoomed range.
- Got: only the name, time and duration of each row.
- Workaround: `--json` piped through python, reading `fields[].value` for `cpuPercent`, `idle_pct` and `iowait_pct`.
