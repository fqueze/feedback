## Question: "how many ms after the job's first launch failure did this test start?"

- Command: `profiler-cli thread markers --search test_breakpoint-14.js --list --limit 0 --session <s>`
- Expected: start times precise enough to compare markers a few hundred ms apart.
- Got: past 60 s, times print as `t=2m13s` (whole seconds), and the "will retry" INFO 32 ms after the test start shows the same `t=2m13s`. `marker info` also prints `Time: 2m13s`.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`, for each of six profiles.
- Could have shown: millisecond start times in the `--list` rows (e.g. `t=133.054s` or `2m13.054s`), as it already does below 60 s (`t=46.391s`).
