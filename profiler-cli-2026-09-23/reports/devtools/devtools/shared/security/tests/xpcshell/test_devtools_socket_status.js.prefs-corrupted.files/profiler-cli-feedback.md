## Marker times past 60 s lose their precision

- Command: `profiler-cli thread markers --session <s> --search socket_status --list --limit 0`
- Expected: `t=59.640s` for the retry's `test` marker, as markers under 60 s get (`t=53.329s`).
- Got: `t=1m` for every marker between 59.5 s and 1m 0.5 s, so the retry's markers all read `t=1m` and cannot be ordered or quoted. The same in TNpgxvmV (`t=1m3s`, `t=1m4s`).
- Workaround: `profiler-cli marker info m-153 --json` and read `start`. One extra call per marker quoted.

## Which marker index is this handle? (review-test_devtools_socket_status.js.prefs-corrupted)

Question: does link `marker=N` point at the marker the report quotes, for about 30 markers across 3 profiles?

- Command: `profiler-cli thread markers --session <s> --list --json --limit 0`
- Expected: each `flatMarkers[]` entry carrying `markerIndex`, as `marker info --json` does, so one dump checks every link.
- Got: `handle`, `name`, `start`, `data`, no `markerIndex`. So I ran one `marker info <m> --json` per link. Neighbours at the same millisecond (e.g. m-964 is 902 and m-965 is 903 in eLE855) have to be told apart one call at a time.
- Could have shown: `markerIndex` in the list JSON, or a `marker find --index N`.
