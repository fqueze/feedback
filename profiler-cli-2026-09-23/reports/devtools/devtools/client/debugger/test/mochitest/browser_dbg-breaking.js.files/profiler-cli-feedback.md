## Question: was the machine idle or saturated while the test waited (resource-usage profile)?

- Command: `profiler-cli thread markers --session browser_dbg-breaking.js-1 --search "name:CPU Use" --list --limit 0` (after `zoom push` on the wait interval)
- Expected: the CPU percent of each `CPU Use` marker in the list, or a summary (mean / p95 / max of `cpuPercent`) for the zoomed range.
- Got: one row per marker with name, time and duration only; the `cpuPercent` field is only visible per marker with `marker info`. The profile has no counters (`counter list`: "No counters in this profile"), so there is no track to summarize either.
- Workaround: `--json`, then a Python script averaging `data.cpuPercent` in 5 s buckets and over the wait interval.
- What would have answered it: `thread markers --search "name:CPU Use"` (aggregate mode) printing min/mean/max of `cpuPercent` over the view, or the list rows showing the payload's main field.
