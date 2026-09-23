# profiler-cli feedback (test_stepping-06.js)

## Question: "what was the whole machine's CPU use, per few seconds, around t?" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0 --json`, then a Python script bucketing `data.cpuPercent` per 2 s.
- Expected: a built-in summary of the `CPU Use` markers over a range (e.g. `thread markers --search 'CPU Use' --bucket 2s` or a CPU line in `profile info` for resource-usage profiles, which today says "No significant activity").
- Got: one marker per ~100 ms; no aggregation. `counter list` says "No counters in this profile."
- Workaround: script over --json, run on each of 6 profiles.

## Question: "how many tests started after time T passed / timed out, and does each TIMEOUT have a matching harness log line?"
- Command: `thread markers --search 'status:TIMEOUT' --list --limit 0 --json` and `--search 'not killing' ...`, compared in Python.
- Expected: `--group-by field:status` restricted to markers starting after T (zoom push T,end includes markers that started before T and overlap).
- Got: group-by works on the whole range; no "starts within range" option.
- Workaround: script over --json.
