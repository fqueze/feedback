# profiler-cli feedback (test_stepping-03.js)

## Question: when exactly did this marker start, to the millisecond?
- Command: `profiler-cli thread markers --session <s> --search test_stepping-03 --list --limit 0` and `profiler-cli marker info m-1 m-3 ...`
- Expected: start times precise enough to compare a test's start with its "will retry" line 31 ms later (e.g. `t=133.485s`).
- Got: `t=2m13s` and `t=2m14s` in both the list and `marker info` (`Time: 2m13s - 2m59s (45.036s)`). Only the duration had ms precision.
- Workaround: `--json` and read `start`. `--list` and `marker info` could print ms (`2m13.485s`) at least when the profile is over a minute long.

## Question: how many tests passed / timed out among those started after time X?
- Command: `profiler-cli thread markers --category Tasks --list --limit 0 --json`, then a Python script grouping `Test` markers by `data.status` before and after the first `Failed to launch` marker.
- Expected: something like `thread markers --search type:Test --group-by field:status` restricted to a zoom that starts at X (the zoom covers markers overlapping it, not starting in it, so it would also count the long tests already running).
- Got: no way to group by marker start time within a range from the default output.

## Question (review-test_stepping-03.js): how busy was the machine between t=136 s and t=180 s?
- Command: `profiler-cli zoom push 136,180 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use"` on a resource-usage profile (`counter list` says "No counters in this profile").
- Expected: min / median / max of the `cpuPercent` field over the zoomed range, or a sparkline like `counter list` gives.
- Got: only the markers' interval durations (`min=63ms, avg=100ms, max=125ms`), nothing about their values.
- Workaround: `--list --limit 0 --json`, then a Python script over `data.cpuPercent`. The reviewed report had misread this by hand (it said 0-3% with two outliers; 20 of 440 samples were above 10%).
