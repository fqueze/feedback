## Question: "exactly when, to the millisecond, did this test start relative to the first launch failure?"

- Command: `profiler-cli thread markers --session <s> --search test_sessionDataHelpers --list --limit 0` and `profiler-cli marker info m-1 m-3 ...`
- Expected: start times precise enough to order events that are 1-40 ms apart (e.g. `t=133.417s`).
- Got: `t=2m13s` in the list and `Time: 2m13s - 2m58s (45.041s)` in `marker info`: second resolution, so the test start, its `will retry` line 40 ms later, and the neighbouring tests 1-4 ms apart all read the same.
- Workaround: `--json` and read `flatMarkers[].start`.
- What the default output could show: millisecond start times (at least when the profile is under ~10 minutes, or when --list rows collide on the same second).

## Question: "how many tests timed out, and did they all start in one burst after event X?"

- Command: `profiler-cli thread markers --session <s> --search "name:test,TIMEOUT" --list --limit 0 --json` piped to a Python script counting markers and their start/duration range before and after the first `Failed to launch` marker.
- Expected: a way to get count + start-time range + duration range of a filtered marker set, optionally split at a time.
- Got: the aggregated (non --list) view gives count and duration stats, but no start-time range; --list gives 487 to 1,060 rows.
- Workaround: script over --json.
- What the default output could show: in the aggregated view, per marker name, the first and last start time alongside the existing duration stats.
