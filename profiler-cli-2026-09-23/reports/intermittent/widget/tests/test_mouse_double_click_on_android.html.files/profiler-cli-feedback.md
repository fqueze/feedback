## Question: was the machine busy while a test hung (mean/max of a numeric marker field over a range)

- Command: `profiler-cli zoom push 170,490 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: some summary of the `cpuPercent` field over the zoomed range (mean / max / percentiles), e.g. in the aggregated (non `--list`) view of `thread markers --search "name:CPU Use"`.
- Got: the aggregate only gives marker count and interval durations; the list gives 3,000+ rows, each needing `marker info` to see the percentage.
- Workaround: `--list --limit 0 --json`, then a Python script averaging `data.cpuPercent` per 10 s bucket.
- Could have shown: per-field stats for numeric payload fields in the aggregated view (or a `--stats <field>` flag), which answers "idle or saturated during the timeout" in one command on resource-usage profiles.
