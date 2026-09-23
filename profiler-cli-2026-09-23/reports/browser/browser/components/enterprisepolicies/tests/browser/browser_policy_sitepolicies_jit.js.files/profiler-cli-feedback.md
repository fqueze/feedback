## `marker info` / `--list` round times to the second or minute on long profiles

- Command: `profiler-cli marker info m-2125 m-2106 m-1543 --session browser-jit-1` (a 5-minute profile)
- Expected: each marker's time to the millisecond, since the point is to order events and quote them.
- Got: `Time: 3m (instant)` for markers at 180.138 s, 180.486 s and 180.961 s; in `thread markers --list`, `t=4m49s` for two markers 1 ms apart. Three different events read as simultaneous.
- Workaround: `marker info ... --json` and read `.markers[].start` (ms) with a script.
- Question the default output did not answer: "at what time, to the ms, did this marker happen?"

## (review) Same rounding, seen again from `thread markers --list`

- Command: `profiler-cli thread markers --session browser-review-browser_policy_sitepolicies_jit.js-1 --search "Entering test test_pageinfo" --list --limit 0`
- Expected: ms times, to check the report's quoted t=180.138 s against the marker.
- Got: `t=2m58s`, `t=2m59s`, `t=3m`; had to re-run with `--json` and a script (`review-dump.py`) to read `.flatMarkers[].start`, and `marker info --json` for `markerIndex`.
- Question the default output did not answer: "is this the marker at t=180.138 s with index 1328067?" Showing ms times and the marker index in `--list` would answer it directly.
