## Marker times lose sub-second precision past one minute (browser_ext_port_disconnect_on_window_close.js)

- Command: `profiler-cli thread markers --category Test --search port_disconnect_on_window_close --list --limit 0 --session ...` and `marker info m-6 m-8 m-13`
- Expected: times to the ms, as for profiles under a minute (`t=9.640s`).
- Got: `t=3m13s` for entering, leaving and checking-for-popups alike, and `t=3m22s` for the failure, so neither a test's duration nor a zoom range can be read from it.
- Workaround: `--json` and read `flatMarkers[].start`.

## Question: when did each group of markers start and end?

- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --group-by field:innerWindowID`
- Expected: per group, the count plus the first and last marker time (and handles), to see which window has been ticking since when.
- Got: count and example handles only.
- Workaround: `--list --limit 0 --json` piped through a Python grouping script.

## Question: which marker index is this handle? (review of browser_ext_port_disconnect_on_window_close.js)

- Command: `profiler-cli marker info m-8508 --session ...`
- Expected: the marker's `markerIndex`, the `marker=N` a profiler.firefox.com link carries, to check that a link points at the quoted marker.
- Got: type, category, time (`2m29s`), thread and fields, but no index; only `--json` has `markerIndex` and the ms `start`.
- Workaround: `marker info m-a m-b --json` piped through Python for `markerIndex` and `start`.

## `--session` rejected before the subcommand

- Command: `profiler-cli --session <id> thread list`
- Expected: a global option, accepted in any position.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
