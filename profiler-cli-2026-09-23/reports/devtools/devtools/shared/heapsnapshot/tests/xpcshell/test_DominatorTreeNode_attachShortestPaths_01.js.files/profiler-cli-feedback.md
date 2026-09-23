## Question: "how many tests passed / timed out, among those that started before vs after time T" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json`, then a Python script bucketing `test` markers by status (label prefix) and by `start`.
- Expected: `thread markers --search name:test --group-by field:status` with a zoom restricted to markers *starting* in the range. `zoom push 50,133` includes markers that overlap the range, so PASS markers started at t=39s showed up as "after the break".
- Could have shown: a per-status count for markers starting inside the zoom (e.g. `--starting-in-range`), or `--group-by field:status` with counts.

## Question: "average machine CPU over a time range" (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` plus a script averaging `data.cpuPercent` over [a,b).
- Expected: `counter info` or `thread markers --search "name:CPU Use"` in a zoom to print avg/min/max of cpuPercent. The profile has no counters ("No counters in this profile"), so CPU is only in markers.
- Could have shown: aggregated numeric-field stats (avg/min/max) per marker group within the zoom.

## Minor: marker times shown as `1m38s` without milliseconds
- Command: `profiler-cli marker info m-6 --session <s>` printed `Time: 1m38s (instant)`; the ms (98.157s) needed `--json`. Past 60 s, the text output loses the precision needed to order events.

## Question (review-test_DominatorTreeNode_attachShortestPaths_01.js): "which handle is the marker at index N" (checking a `marker=N` link)
- Command: `profiler-cli thread markers --session <s> --list --limit 0 --json`, then `marker info <20 candidate handles> --json` and a script to find `markerIndex` 10730.
- Expected: `thread markers --json` `flatMarkers[]` to carry `markerIndex`, or `marker info --index 10730`.
- Got: `flatMarkers[]` has `handle` but no `markerIndex`, so a link's `marker=N` can only be matched by guessing candidates from its time and text.
