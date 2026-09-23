## Marker times are rounded to the second in text output

- Command: `profiler-cli marker info m-153 --session <s>` (and `thread markers --list`)
- Expected: a start time precise enough to zoom next to it (ms), e.g. `t=94.155s`.
- Got: `Time: 1m34s (instant)`; every marker in a 1 s window reads the same, so ordering against other markers and zooming right after the last test log line needs `--json` (`"start": 94155.14`).
- Workaround: `marker info --json` and read `start`.
