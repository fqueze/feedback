## `--min-duration 0` silently drops instant markers

- Command: `profiler-cli thread markers --search "highlighter-event,setRuleText,..." --min-duration 0 --list --limit 0 --session cssshape_07-2`
- Expected: every matching marker (0 ms is no filter), including the instant `DevTools:RDP Front customhighlighter.highlighter-event` markers.
- Got: only interval markers; the instant ones were gone, with no note that instants were excluded. It looked as if the events had not happened.
- Workaround: drop `--min-duration`. The header could say "N instant markers excluded by --min-duration".

## Which marker index a handle is, to check a link's `marker=N` (review)

- Question: does the `marker=N` in a report's link point at the marker it quotes? 33 links to check across two profiles.
- Command: `profiler-cli marker info m-46 --session review-cssshape_07-1`
- Expected: the marker's index (the `marker=` value of its profiler URL) in the default output, or a `thread markers --list` column for it.
- Got: type, time, thread and fields only; the index is in `--json` (`markerIndex`) alone, so I looped a python one-liner over `marker info <h> --json` for each handle.
- Could have shown: `Index: 72174` under `Thread:` in `marker info`, or accept `marker info --index 72174` to go from a link straight to the marker.
