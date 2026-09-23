## Marker times printed to the second in a 3-minute profile

- Command: `profiler-cli thread markers --session <s> --category Test --search <test> --list --limit 0` (and `marker info m-44`)
- Expected: marker times precise enough to order the test's steps against other threads' markers (ms, e.g. `t=172.089s`).
- Got: `t=2m52s` for every marker in that second; `marker info` also prints `Time: 2m52s`. Several steps of the test (Leaving test, Removing tab, checking for open popups) and the start of a Renderer hang fall in the same one or two seconds and cannot be ordered.
- Workaround: `--json` and a script printing `start/1000`, for every list I needed (test log, Renderer markers around the hang start, runs of `RefreshDriverTick waiting for paint`).
- Question the default output could not answer: "in which order, to the millisecond, did these markers happen?" Printing seconds with 3 decimals once a profile is longer than a minute would answer it.

## Runs of a repeated instant marker

- Question: "from when to when did `RefreshDriverTick waiting for paint` fire continuously?" (291 instant markers in a 5.7 s window).
- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --list --limit 0 --json` piped to a script grouping markers less than 40 ms apart.
- The default output shows the first 30 rows at second resolution, or a count; neither says where the continuous run starts and stops, nor where its gaps are.
