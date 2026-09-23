# profiler-cli feedback (test_nesting-03.js)

## Question: "was the machine CPU-saturated while this test ran?" (resource-usage profile)
- Commands: `profiler-cli profile info`, `profiler-cli counter list`, then `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a script.
- Expected: `profile info` / a zoomed `counter info` to give the CPU use over the test's range.
- Got: `profile info` says "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile.", while the profile holds 7,369 `CPU Use` markers showing 100% CPU. That "no activity" line is misleading for these profiles. Had to average the markers' `idle_pct` over the range myself. A summary of CPU Use markers over the current zoom (average/min) would answer it.

## `--json` flat markers omit `duration` for instant markers
- Command: `thread markers --search test_nesting-03 --list --limit 0 --json`, reading `m['duration']`
- Got: KeyError on instant markers. Minor; `null` would be friendlier to scripts.
