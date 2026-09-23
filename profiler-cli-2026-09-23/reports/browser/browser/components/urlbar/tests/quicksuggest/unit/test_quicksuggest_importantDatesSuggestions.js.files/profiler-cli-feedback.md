## Question: average machine CPU use over a time range (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0` (after `zoom push 42,95`)
- Expected: some summary of the `CPU Percent` field over the zoomed range (duration-weighted mean, or per-N-second buckets), since that is the question the resource-usage profile's CPU markers answer ("was the machine saturated during this test?").
- Got: 442 rows of `CPU Use` markers without their value in the list line; each value only via `marker info`. `counter list` says "No counters in this profile".
- Workaround: `--json` and a Python script to weight `data.cpuPercent` by duration in 5 s buckets.
