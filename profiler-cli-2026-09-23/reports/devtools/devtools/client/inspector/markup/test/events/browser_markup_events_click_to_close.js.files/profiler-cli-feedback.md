## review-browser_markup_events_click_to_close.js: which `marker=N` is this handle?

- Question: does a report link's `marker=N` point to the marker it quotes? The review brief asks for exactly this check.
- Command: `profiler-cli marker info m-109 --session <s>`. The text output shows type, time, fields and stack, but not the marker index.
- Workaround: `marker info m-N --json | python3 -c '...markerIndex...'` for each of about 15 markers.
- What would have answered it: an `Index: 321646` line in the default `marker info` output, or an option on `thread markers --list` to show the index next to each handle.
