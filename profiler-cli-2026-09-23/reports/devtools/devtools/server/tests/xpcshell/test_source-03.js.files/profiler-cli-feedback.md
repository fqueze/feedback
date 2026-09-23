## Marker times past one minute are printed to the second

- Command: `profiler-cli thread markers --session test_source-03.js-2 --search test_source-03 --list --limit 0`
- Expected: start times precise enough to order markers that are tens of ms apart (the xpcshell resource-usage profile is minutes long, and the question was "how long after the test's start did the harness log `will retry`").
- Got: `t=2m13s` for both the `test` marker and the `will retry` INFO 37 ms later, and `t=2m12s` for the launch failure 1.8 s earlier; below one minute the same command prints `t=46.851s`. `marker info` also prints `Time: 2m13s - 2m59s`.
- Workaround: `--json` and read `start` (ms). The text output could keep millisecond precision past a minute (e.g. `2m13.463s`).
