## Marker list timestamps are rounded to whole seconds

- Question: at what millisecond did each test log line (popupshown PASS, the failure) happen, to line them up with DOMEvent markers?
- Command: `profiler-cli thread markers --category Test --search <subtest> --list --limit 0 --session <id>`
- Expected: `t=175.548s` or similar, millisecond precision (a 5 s wait window and a 30 ms throttle need it).
- Got: `t=2m56s` for every row; the only way to get the ms was `--json` and a Python script over `flatMarkers[].start`. `marker info` also prints `Time: 2m56s`.
- Workaround: `--json` + script.
