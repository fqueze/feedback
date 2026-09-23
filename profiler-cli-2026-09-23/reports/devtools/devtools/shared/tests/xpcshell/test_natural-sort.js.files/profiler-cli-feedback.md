## Marker start times lose precision past one minute
- Question: how many ms after the test's `test` marker started was its "will retry" line logged (it tells a launch that raised from a process that ran)?
- Command: `profiler-cli thread markers --session <s> --search natural-sort --list --limit 0`
- Expected: start times to the ms, e.g. `t=65.878s`.
- Got: `t=1m6s` for both markers, so the 12 ms gap is invisible.
- Workaround: `--json` and read `flatMarkers[].start`.

## No aggregate over a numeric marker field
- Question: what was the machine's average CPU use during this test's 45 s, and in the 8 s before the onset?
- Command: `profiler-cli zoom push 65.878,110.886` then `thread markers --search "CPU Use"` gives counts and durations only.
- Expected: min/avg/max of `cpuPercent` over the matching markers in the range.
- Workaround: `--list --limit 0 --json` and a Python average over `data.cpuPercent`.

## Start range of the markers matching a search
- Question: when did the job's 750 TIMEOUT tests start (first and last), and when did the last PASS test start?
- Command: `profiler-cli thread markers --session <s> --search status:TIMEOUT`
- Expected: the aggregate row to show the first/last start time next to the duration stats.
- Got: count and duration stats only.
- Workaround: `--list --limit 0 --json`, sort by `start`.
