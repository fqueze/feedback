## Question: how busy was the machine during this time window? (resource-usage profile)

- Command: `profiler-cli zoom push 824,842 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percentage of each `CPU Use` marker in the list, or a summary of it (mean/min/max over the zoom). The resource-usage profile has no counters, so `counter info` cannot answer this.
- Got: one row per marker with only name, time and duration. The `CPU Percent` field is visible only through `marker info`, one marker at a time. There are about 8 markers per second.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per second. A per-field aggregate for numeric payload fields in the `thread markers` summary (for example "CPU Percent: mean 97%, min 87%, max 100%") would answer this directly.
