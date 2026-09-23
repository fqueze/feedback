## Question: what was the machine's CPU use during one test, from the resource-usage profile?
- Command: `profiler-cli zoom push m-2` (the test's marker), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python mean over `data.cpuPercent`.
- Expected: `counter list` / `profile info` to expose CPU use (it says "No counters in this profile" and "CPU activity: No significant activity"), or the marker aggregate to give mean/min/max of a numeric field.
- Got: 336 individual markers, one per 100 ms, each needing `marker info` to read.
- Could have shown: `thread markers --search "name:CPU Use"` in aggregate mode reporting mean/min/max of `cpuPercent` (and iowait) over the zoomed range.

## Question: which marker does a link's `marker=N` point to? (review)
- Link had `marker=28549`; the quoted marker (`Starting test_a_deletion_that_fails_fails_the_copy`) turned out to be index 30868.
- Workaround: `zoom push` a 20 ms window, then a shell loop of `marker info m-X --json` over every handle to read `markerIndex`, until one matched (it was the preceding `task` marker).
- Could have shown: `marker info --index 28549` (or `load` of a link with `marker=` printing the m-handle it resolved to), or `markerIndex` in `thread markers --list` rows.
