## Question: the exact time of a marker, to pass to `screenshots --at`

- Command: `profiler-cli marker info m-11 m-12 --session ...`
- Expected: a start time precise enough to reuse (e.g. `80.104s`).
- Got: `Time: 1m20s (instant)` — rounded to the second, and the `--list` rows also say `t=1m20s` for every marker of the test, so two failures 320 ms apart could not be told apart or targeted.
- Workaround: `thread markers --list --json` and reading `flatMarkers[].start` (ms). The default output could print times with millisecond precision (at least in `marker info`), or `screenshots` could accept a marker handle (`--at m-11`).
