## Question: how busy was the whole machine during a timeout, in a resource-usage profile

- Commands: `profiler-cli profile info --session test_census_filtering_02.js-1` and `profiler-cli counter list --session test_census_filtering_02.js-1` (resource-usage profile of task PyUxuOpdQj2b3T3XyQOKpg).
- Expected: a CPU-over-time view of the machine, as the brief says the resource-usage profile holds "the CPU use of the whole machine".
- Got: `profile info` says "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile.". The machine CPU is only in 1237 `CPU Use` markers (`cpuPercent` as a string like "77.1%").
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a Python script that bucketed `cpuPercent` per 5 s. What would have answered it: `profile info` (or `counter list`) showing the `CPU Use` markers as a machine CPU sparkline, or `thread markers --search "name:CPU Use"` printing min/mean/max of `cpuPercent` for the zoomed range.

## (review) `marker info` returns a different JSON shape for a range than for a list of handles

- Commands: `profiler-cli marker info m-4200..m-4211 --session review-test_census_filtering_02.js-1 --json` returns a JSON array of records; `profiler-cli marker info m-5958 m-5961 m-222 --session review-test_census_filtering_02.js-1 --json` returns `{"type": "marker-info-multi", "markers": [...]}`.
- Expected: the same shape for both, since both are "several markers in one call".
- Got: a script written for one shape failed with `TypeError: string indices must be integers` on the other, twice.
- Workaround: handle both shapes. The question behind it was "which `markerIndex` do these handles have", to check a report's `marker=N` links; a `markerIndex` column in `thread markers --list` would have answered it without `--json`.
