## `thread markers --list` start times have 1-second resolution

- Question: at which millisecond did each test log line, network request and RDP request happen, to order a 4 s timeout against them.
- Command: `profiler-cli thread markers --category Test --search browser_inspector_reload_iframe --list --limit 0 --session <s>`
- Expected: start times precise enough to order events (e.g. `t=368.228s` or `6m8.228s`).
- Got: `t=6m8s` for every row within the same second; dozens of the rows that mattered (reload start, querySelector, target-available, DOMContentLoaded) all read `t=6m8s`.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`, for every list in the investigation (about 10 times).

## `--json` flatMarkers carry no end or duration

- Question: when did the `RDP Front` request / `Network` load end.
- Command: `profiler-cli thread markers --search "name:RDP Front" --list --limit 0 --json --session <s>`
- Expected: `end` (or `duration`) on interval markers, as the text list shows a duration.
- Got: keys `handle, name, label, start, hasStack, category, markerType, fields, data` only.
- Workaround: ran the same query twice, text for durations and JSON for starts, and joined them by eye.
