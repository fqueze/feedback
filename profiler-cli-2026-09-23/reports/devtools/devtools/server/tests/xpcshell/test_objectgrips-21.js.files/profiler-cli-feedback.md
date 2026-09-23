# profiler-cli feedback (test_objectgrips-21.js)

## Question: what was the whole-machine CPU use over a time range of a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 1204,1237`)
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean, min, max, or a per-second sparkline), since resource-usage profiles have no samples or counters and `profile info` / `thread info` say "No significant activity".
- Got: one row per marker with only its duration; the percentage is only in `marker info` or `--json`.
- Workaround: `--json` and a Python script computing a duration-weighted mean per 2 s bucket.
