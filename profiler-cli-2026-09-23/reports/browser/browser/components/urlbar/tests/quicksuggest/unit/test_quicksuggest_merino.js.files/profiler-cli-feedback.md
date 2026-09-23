## Question: "was the machine busy during this range?" (resource-usage profile)

- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0 --json` after `zoom push m-50`, then a Python script averaging `data.cpuPercent`.
- Expected: some summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean/max), e.g. in `thread markers` aggregated stats or `profile info`'s CPU section. The resource-usage profile has no counters (`counter list` says none), so `profile info` reports "No significant activity" for it.
- Got: only per-marker values; needed a script to learn "mean 1.8%, max 34.5% during the 2-minute timeout".

## `zoom push 11m50s,13m11s` did not zoom to what it said

- Command: `profiler-cli zoom push 11m50s,13m11s --session S` (the times as `thread markers` prints them).
- Expected: an 81 s range, or an error if the `NmNs` form is not accepted.
- Got: "Zoom depth: 1", and the next query returned 21 `CPU Use` markers (about 2 s) instead of ~810. `zoom push 710,791` gave the right range. Workaround: convert to seconds by hand.

## Question: "how long did each step take, between two recurring log lines?"

- Command: `thread markers --search forceSync --category Test --list --limit 0 --json` plus a script pairing "Syncing Rust backend" with the following "Done syncing Rust backend".
- Expected: nothing built in, but worth noting: a per-test log is full of start/done INFO pairs, and "time between marker A and the next marker B" is a frequent question.

## Question: "what fraction of the main thread's wall-clock samples is in `__semwait_signal`?" (review)

- Command: `profiler-cli thread samples --include-idle --session S` and `thread samples-top-down --include-idle --json` on the EZts per-test profile, full range, no filters.
- Expected: counts over the thread's 2184 samples (`thread info` says 2184), so that 1657 in `__semwait_signal` could be checked.
- Got: "217 running samples" in `thread samples` (176 in `__semwait_signal`, 81%), and `totalSamples: 6` at the top-down root with `weightType: samples`. Neither matches the sample table, and `--include-idle` changed nothing. Workaround: downloaded the artifact and counted leaf addresses by library offset in Python.
