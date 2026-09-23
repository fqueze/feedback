## Question: was one core pegged, or the machine idle, during a window of a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use"` (after `zoom push 150,560`) on a Marionette job's `profile_resource-usage.json`.
- Expected: some summary of the CPU Use payload values (cpuPercent min/avg/max) over the zoomed range, or per time bucket.
- Got: only counts and durations of the markers; the CPU percentages are only in the per-marker payload.
- Workaround: `--list --limit 0 --json` piped into a Python script bucketing `data.cpuPercent` per 10 s. That showed min CPU pinned at exactly 12.5% (1 of 8 cores) for the whole stall, which the default output could have shown as a min/avg/max over the range or as a sparkline like `counter list` has.
- Also: `profile info` says "No significant activity" and `counter list` "No counters" for this profile, although the CPU Use markers carry the whole machine's CPU use.
