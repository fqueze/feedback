## Question: "at what exact time was this marker" (past the first minute)

- Command: `profiler-cli marker info m-6 --session …` (and `thread markers --list`)
- Expected: a millisecond timestamp, e.g. `t=98.235s`.
- Got: `Time: 1m38s (instant)`; past 60 s both views round to the second, so the 16 ms between the log replay and the ERROR, or the 19 ms between a test's start and its `will retry`, cannot be read.
- Workaround: `marker info --json` and read `start`.

## Question: "how many tests of each status started before / after time t"

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python script grouping `data.status` by `start` < / >= 50.35 s.
- What would answer it: a start-time-only range filter combined with `--group-by field:status` (`zoom push` keeps markers that overlap the range, so the 45 s TIMEOUTs that start after t still count in a "before t" zoom).

## Question: "average machine CPU over a time range" in a resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, then a duration-weighted average of `data.cpuPercent` (a string like `"17.9%"`) over 53.21–98.23 s.
- What would answer it: an average/min/max of `cpuPercent` over the current zoom in the `CPU Use` stats, or exposing it as a counter so `counter info` works (`counter list`: "No counters in this profile").

## Question: "which handle is marker index N" (review of a link's `marker=N`)

- Command: `profiler-cli marker info m-4329..m-5477 --json --session review-test_HeapSnapshot_takeCensus_03.js-1`, to find which of the 1,149 `not killing` markers has `markerIndex` 10863.
- Got: `Marker range ... covers 1149 handles, more than the maximum of 256`.
- Workaround: narrowed by time from the `--list --json` output, then passed ~130 handles one by one.
- What would answer it: `marker info` accepting a marker index (e.g. `#10863`), or `markerIndex` in `thread markers --list --json` rows.
