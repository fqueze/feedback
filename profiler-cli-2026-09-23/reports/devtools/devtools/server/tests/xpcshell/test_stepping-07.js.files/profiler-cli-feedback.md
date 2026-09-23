## Marker times past 1 minute are rounded to the second in `--list` and `marker info`

- Command: `profiler-cli thread markers --session test_stepping-07.js-1 --search test_stepping-07 --list --limit 0` (and `marker info m-1 m-3`)
- Question: how long after this test's start did the harness log `failed or timed out, will retry.`? (It tells a launch failure, ~40 ms, from a test that ran.)
- Expected: millisecond start times, e.g. `t=133.496s` and `t=133.535s`.
- Got: `t=2m13s` and `t=2m14s` for both, so the 39 ms gap is invisible; the duration column is precise (`45.041s`), the start column is not.
- Workaround: `--json` and a Python script over `flatMarkers[].start`.
