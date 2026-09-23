## Question: which markers belong to one window (innerWindowID)?

- Command: `profiler-cli thread markers --search innerWindowID:352 --session <s>` (DOMEvent and SetNeedStyleFlush markers carry `innerWindowID` under "Other payload fields (no schema)")
- Expected: the markers of that window, to follow one browser window's occlusionstatechange / visibilitychange / click events when the test has three windows open.
- Got: "No markers match the specified filters."
- Workaround: `marker info m-a m-b ... | grep innerWindowID` on each candidate marker, one by one. A `--search innerWindowID:N` filter, or the innerWindowID shown in the `--list` row, would have answered it directly.

## Question (review): which marker index does this handle have, to check a link's `marker=N`?

- Command: `profiler-cli marker info m-511 m-494 m-491 ... --session <s>`
- Expected: each marker's `markerIndex` (what profiler links' `marker=N` holds) next to its handle, plus its `innerWindowID`, to check a report's links in one call.
- Got: the text output shows neither the marker index nor, for some markers, which window it belongs to in a scannable form; needed `--json` and a python script over `markerIndex` and `rawFields`.
- Workaround: `marker info ... --json | python3 -c ...`. Printing `markerIndex` in the default `marker info` header (and in `--list` rows) would answer it.
