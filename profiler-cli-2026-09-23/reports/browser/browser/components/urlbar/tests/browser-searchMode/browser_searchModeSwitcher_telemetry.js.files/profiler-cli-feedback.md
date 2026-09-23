## Question: at what exact time did each test log line happen, to order them against other markers within a second?

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_searchModeSwitcher_telemetry.js --list --limit 0`
- Expected: a start time precise enough to order markers (ms), as the list is the test's log and the brief says it is "timestamped next to everything else".
- Got: `t=1m58s` for ~100 consecutive lines; the sub-second order against other markers is lost.
- Workaround: `--json | jq '.flatMarkers[] | "\(.handle) \(.start) ..."'`.
- What the output could show: `t=1m58.083s` (ms precision), at least when the list spans less than a few minutes or when consecutive rows share the same rounded value.

