## Question: "how busy was the machine, per 30 s, over the job" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0 --json | python3 ...` (bucketed `data.cpuPercent` by 30 s).
- Expected: a way to see the machine CPU use over time without scripting — e.g. the `CPU Use` series exposed as a counter (`counter list` says "No counters in this profile"), or `thread markers --search 'name:CPU Use'` printing min/avg/max of `cpuPercent` over the zoom range.
- Got: only per-marker rows (about 20,000 of them over 42 min); the aggregated view gives duration stats, not the payload value.
- Workaround: the script above.
