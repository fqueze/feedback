## How busy was the whole machine during a window? (resource-usage profile)

- Command: `profiler-cli zoom push 1143,1162 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: each row showing the machine CPU % (the `cpuPercent` field), or better a summary (mean/min/max CPU %) for the zoomed range.
- Got: 151 rows with only name, time and duration; the CPU value is only in `marker info` one marker at a time. `profile info` on the same profile says "No significant activity" and `counter list` says "No counters in this profile", although the profile holds 14,546 CPU Use markers.
- Workaround: `--json` piped into a Python script averaging `data.cpuPercent` over time windows and per minute.
