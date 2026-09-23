## Exact time of markers past the first minute

- Question: at what exact time (ms) did these markers fire, to order them and measure a 19 ms gap?
- Command: `profiler-cli thread markers --session <s> --search test_HeapSnapshot_takeCensus_05 --list --limit 0`
- Expected: start times with ms precision, e.g. `t=98.235s`.
- Got: `t=1m38s` for every marker after 60 s, so six markers in the same 6 ms all read `1m38s`. `marker info` also prints `Time: 1m38s (instant)`.
- Workaround: `--json` and read `flatMarkers[].start`.

## Status counts of `test` markers before and after a point in time

- Question: how many tests PASS / TIMEOUT / SKIP among those that started before vs after t=50.35 s (the job-wide launch break)?
- Command: `profiler-cli thread markers --session <s> --category Tasks --list --limit 0 --json`, then a Python script grouping `data.status` by `start`.
- What could have shown it: `thread markers --search name:test --group-by field:status` inside `zoom push 50.35,end` would, if a zoom counted markers by start time rather than overlap. A zoom includes every interval marker overlapping the range, so long-running tests from before the break are counted too.
