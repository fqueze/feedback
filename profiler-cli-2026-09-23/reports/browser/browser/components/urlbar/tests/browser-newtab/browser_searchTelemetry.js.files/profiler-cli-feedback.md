## Marker times in `--list` and `marker info` are rounded to the second

- Command: `profiler-cli thread markers --category Test --search browser_searchTelemetry.js --list --limit 0 --session <s>`, then `profiler-cli marker info m-56 m-57 m-16 --session <s>`
- Expected: times with millisecond precision, so that ordering and gaps between consecutive test-log markers (43 ms here between the last TEST-PASS and the TEST-UNEXPECTED-FAIL) can be read.
- Got: `t=1m53s` for every marker in a 2 s span, in both the list and `marker info` (`Time: 1m53s (instant)`).
- Workaround: `--json` and a script printing `flatMarkers[].start`.
- Question the default output did not answer: "how far apart are these consecutive markers?"
