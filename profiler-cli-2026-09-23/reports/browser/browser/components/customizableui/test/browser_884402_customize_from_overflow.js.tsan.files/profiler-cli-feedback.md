## Question the default output did not answer: "how busy was the machine over this range?"

- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0` on a resource-usage profile, zoomed to 102.0–108.1.
- Expected: some summary of the `CPU Percent` field over the range (min/max/mean), since the resource-usage profile has no counters (`counter list` says "No counters in this profile").
- Got: 62 rows with no field values; `marker info` on one row shows the value, one marker at a time.
- Workaround: `--json` and a Python one-liner averaging `data.cpuPercent`.
