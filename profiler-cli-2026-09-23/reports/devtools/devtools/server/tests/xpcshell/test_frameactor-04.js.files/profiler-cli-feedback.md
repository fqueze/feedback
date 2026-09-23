# profiler-cli feedback (test_frameactor-04.js)

## Question: "did these markers happen in the same millisecond?" (times past 1 minute)

- Command: `profiler-cli thread markers --session test_frameactor-04.js-1 --search test_frameactor-04 --list --limit 0`
- Expected: marker start times with ms precision, as for times under a minute (`t=52.549s`).
- Got: `t=1m38s` for the replayed-log Begin / ERROR / End markers; `marker info` also prints `Time: 1m38s (instant)`. Whether Begin and End of the replayed log fall in the same ms (i.e. the log is empty) cannot be read.
- Workaround: `--json` and read `flatMarkers[].start` (97602.165 for all three).

## Question: "how many tests timed out, and when did they start?"

- Command: `profiler-cli thread markers --search "name:test,TIMEOUT" --list --limit 0 --json | python3 ...` (filter `name == "test"` and label starting with `TIMEOUT`, then min/max of `start`).
- The default output groups by name only; a `--group-by field:status` summary with start-time range per group (or `--search status:TIMEOUT` returning only `test` markers, with a count and first/last time) would have answered it directly.
