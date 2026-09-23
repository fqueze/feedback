# profiler-cli feedback (test_watchpoint-05.js)

## Question: "at what millisecond did this marker start, in a profile several minutes long?"

- Command: `profiler-cli marker info m-1 m-3 --session test_watchpoint-05.js-1`
- Expected: start and end with ms precision (I needed the 48 ms gap between a test starting and its "will retry" line, and to order markers against a breakdown 2 s earlier).
- Got: `Time: 2m14s - 2m59s (45.040s)` for both markers, rounded to the second. `thread markers --list` has the same `t=2m14s` rounding.
- Workaround: `marker info --json | jq .markers[].start`.
- Could show: `2m13.592s`, or seconds with ms, in the plain output once the profile is longer than a minute.

## Question: "what was the machine's average CPU over this time range?" (resource-usage profile)

- Command: `profiler-cli zoom push 133.592,178.632` then `profiler-cli thread markers --search "name:CPU Use"`; `profiler-cli counter list` says "No counters in this profile."
- Expected: an average (time-weighted) of the `cpuPercent` field of the `CPU Use` markers in the zoomed range.
- Got: only the marker count and interval stats (451 markers, avg 100 ms).
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then jq + awk for a duration-weighted mean per range.
- Could show: min/avg/max of numeric payload fields in the aggregate view, or a `--stats field:cpuPercent` option.

## Question: "how many tests started after time X, by status?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0` (1,113 markers mixing `Test` and `Text` markers named `test`).
- Expected: a count by `status` of the `Test` markers starting after the job's first launch failure.
- Got: a flat list; `zoom push` would also include markers that only overlap the range.
- Workaround: `--json`, jq to a TSV of start/duration/status, awk to count.
- Could show: `--group-by field:status` combined with a "starting in range" option for zoom.
