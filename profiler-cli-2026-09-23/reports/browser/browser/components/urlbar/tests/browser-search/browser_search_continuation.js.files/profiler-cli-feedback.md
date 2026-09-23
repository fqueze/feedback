## `thread markers --list` prints times rounded to the second past one minute

- Command: `profiler-cli thread markers --category Test --search browser_search_continuation --list --limit 0 --session <id>`
- Expected: each row's start time with millisecond precision (e.g. `t=84.123s`), since the list exists to order events.
- Got: `t=1m24s` for every row of the test (70 rows over ~1.5 s), so ordering a TEST-PASS against a `NotifyObservers` or a `DOMEvent` in the same second is impossible from the default output.
- Workaround: `--json` and read `flatMarkers[].start` in a script.
## `thread markers --list` does not show resource-usage marker values

- Question: machine CPU % and bytes written per sample over a range of a resource-usage profile.
- Command: `profiler-cli thread markers --session <id> --search 'CPU Use,IO' --list --limit 0` after `zoom push 952.5,958.0`
- Expected: each `CPU Use` row with its `cpuPercent`, each `IO` row with `write_bytes`/`write_count`, as the marker chart tooltip shows.
- Got: rows labelled only `CPU Use` / `IO`, no values, and times rounded to the second (`t=15m57s`).
- Workaround: `--json` and read `flatMarkers[].fields` in a script. (reviewer, browser-review-browser_search_continuation.js)
