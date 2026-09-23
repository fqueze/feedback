## Question: "how busy was the whole machine between t=A and t=B?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list` after `zoom push 100,125`
- Expected: an aggregate of the `CPU Use` markers' cpuPercent over the zoomed range (mean / max), or a counter track I could `counter info`.
- Got: one row per 50-250 ms marker, no values in the row (CPU Percent only in `marker info`), and `counter list` says "No counters in this profile".
- Workaround: `--list --limit 0 --json` and a Python script computing the duration-weighted mean of `data.cpuPercent` per window.
