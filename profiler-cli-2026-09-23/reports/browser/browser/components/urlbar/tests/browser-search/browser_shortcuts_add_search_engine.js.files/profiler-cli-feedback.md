## Marker times lose sub-second precision once a profile is longer than a minute

- Command: `profiler-cli thread markers --search "Link:SetIcon,openNewForegroundTab,browserLoaded,..." --list --limit 0 --session <s>` on a 2m39s profile (task dBEP8hO5Sl6EJHQ1BAARlw, `profile_browser_shortcuts_add_search_engine.js.json`), zoomed to a 60 ms window.
- Expected: start times precise enough to order markers a few ms apart (like `t=2.075s` on short profiles).
- Got: every row printed `t=1m54s`, so the ordering question ("was the icon set before the listener was added?") could not be answered from the text output. `marker info` also prints `Time: 1m54s - 1m54s (22.038ms)`.
- Workaround: `--json` and a Python one-liner printing `flatMarkers[].start` in ms.
- Question the default output did not answer: the relative order and ms gaps of markers inside a zoomed range. Printing ms (e.g. `114263.774ms`, or times relative to the zoom start) when the view is short would have answered it.

## (review) `marker info` does not print the marker index a profiler link's `marker=N` refers to

- Command: `profiler-cli marker info m-9 --session <s>` (profiler-cli 0.9.0), to check that a report's `.../marker-table/?marker=54110` link points at the marker it quotes.
- Expected: the marker's index in the thread's marker table, next to its handle, in the default output.
- Got: name, type, category, time, thread and fields, but no index. Only `--json` has `markerIndex`, so checking the report's ~30 links took a shell loop of `marker info <h> --json | python3 -c ...`, one call per handle.
- Question the default output did not answer: "which marker does `marker=N` in this link point to?". Printing `Index: N` in `marker info`, or accepting `marker info --index N`, would have answered it.
