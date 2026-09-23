## Question: "how many tests timed out, and when did they start relative to the first launch failure?"
- Command: `profiler-cli thread markers --session <s> --search "status:TIMEOUT" --list --limit 0 --json` then a Python script counting `start` before/after a timestamp and the min/max start.
- Expected: the default (non-list) aggregated output for a marker search to give count plus first/last start time, so "1149 markers, starting 52.195s..55.814s" is readable without a script.
- Got: aggregated stats are about durations; the start-time range needed the JSON.
- Workaround: script over `--json`.
