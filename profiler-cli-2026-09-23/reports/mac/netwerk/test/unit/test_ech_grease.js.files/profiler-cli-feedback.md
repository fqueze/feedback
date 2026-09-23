## Question: "what was the whole machine's CPU use while this one test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push 298.07,328.3 --session <ru>` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <ru>`
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean/min/max, or per-bucket like `counter info` prints), since the resource-usage profile carries machine CPU as ~10 `CPU Use` markers per second rather than as a counter (`counter list` says "No counters in this profile").
- Got: 304 rows, one per marker, each needing `marker info` to see its percent; the aggregate view only gives durations.
- Workaround: `--json` and a Python one-liner averaging `data.cpuPercent`.
- What could have answered it: `thread markers --search "name:CPU Use" --stats-field cpuPercent`, or exposing the CPU Use markers as a counter so `counter info` works on them.

## Question: "how much CPU did this process actually get over this range?" (review-test_ech_grease.js)

- Command: `profiler-cli zoom push 0.22,30.18 --session <s>` then `profiler-cli counter info c-1 --session <s>` (macOS arm64 per-test profile, `processCPU` counter).
- Expected: a percent that says what it is a percent of (one core or all cores, and over wall time or over the sample gaps), matching the raw counts.
- Got: 1.4–2.0% per 3 s bucket with no denominator stated. The original report quoted 5.6–7.9% for the same range, about 4× more, and the raw `count` values have no unit in the profile. That left no way to tell which figure was right without downloading the profile and summing `threadCPUDelta` (µs) for the main thread in Python: 22–38 ms per 3 s.
- What could have answered it: `counter info` printing the CPU time per bucket (ms) next to the percent, and saying what the percent is relative to.
