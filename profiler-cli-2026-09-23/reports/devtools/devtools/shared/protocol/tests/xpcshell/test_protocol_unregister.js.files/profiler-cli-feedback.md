## Question: "how many tests ended with each status, and which TIMEOUTs did not last the usual 45 s" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0`
- Expected: a count of `test` markers per `status` field, and a way to spot outlier durations within one status.
- Got: the aggregate groups by marker name only (all 2843 are "test"); the status is only in the label text, so counting needs `--json` and a script.
- Workaround: `--json`, then Python over `flatMarkers[].fields` (status) and `duration`. A `--group-by <field>` on the aggregate, with min/median/max duration per group, would have answered it directly.

## Question: "which markers were logged between these two instant markers"
- Command: `profiler-cli zoom push 98.27,98.32` then `thread markers --list --limit 0`
- Expected: the ~170 markers that start inside the 50 ms window.
- Got: 1082 rows, because every interval marker overlapping the window (the 45 s `test` markers of ~900 tests) is listed too, sorted first; rows show times rounded to `1m38s`, so ordering the instants needed `--json`.
- Workaround: `--json` and filter on `start`. A `--starting-in-range` flag, or millisecond timestamps in the list when the zoom is under 1 s, would avoid it. Also, `flatMarkers[]` entries omit `duration` for instants (not `null`), and the handle key is `handle` there but `markerHandle` in `marker info --json`.
