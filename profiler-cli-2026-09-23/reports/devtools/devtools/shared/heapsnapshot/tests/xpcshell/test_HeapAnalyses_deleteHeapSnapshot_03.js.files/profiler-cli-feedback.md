## Marker times past one minute lose sub-second precision in `--list` and `marker info`

- Command: `profiler-cli thread markers --session <s> --search "early_shutdown.js,Failed to launch" --list` and `profiler-cli marker info m-6 m-7`
- Expected: timestamps precise enough to order two markers 25 ms apart (a test's end at 63.871s and a launch failure at 63.896s).
- Got: both shown as `t=1m4s` / `Time: 47.432s - 1m4s`. Below one minute the output keeps milliseconds (`t=44.598s`); above it, they are dropped.
- Workaround: `marker info --json` and read `start`/`end`.
- Suggestion: keep milliseconds in the `XmY.ZZZs` form (`1m3.896s`).
