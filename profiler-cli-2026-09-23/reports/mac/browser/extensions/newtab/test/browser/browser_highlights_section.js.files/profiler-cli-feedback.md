## Question: in which order did the parent main thread handle these IPC messages, to the sub-millisecond?

- Command: `profiler-cli thread markers --search "name:IPCIn" --list --limit 0 --session S` (zoomed on a 20 ms window)
- Expected: one row per IPCIn with its message type (e.g. `PBrowser::Msg_OnStateChange`), ordered by when the receiving thread handled it, with enough precision to order events 0.2 ms apart.
- Got: rows ordered by send time (the marker start), times rounded to the ms (`t=33.655s`), and an empty description column: the message type only appears in `marker info`.
- Workaround: `--json`, then a script computing `start + duration` and reading `fields[messageType]`, sorted by that end time. Same for `marker info m-a m-b --json` to get sub-ms start/end of a few markers: its output is a stream of JSON objects, not one array, so it needed a `raw_decode` loop.
- What the default output could have shown: the IPC message type in the list row, and a `--sort end` (or receipt-time) option; a higher-precision time column when the zoom range is under ~100 ms.

## Question: which marker does a link's `marker=N` point at? (review-browser_highlights_section.js)

- Command: `profiler-cli thread markers --list --limit 0 --json --session S` on a 2 ms zoom, then `marker info <m> --json` on every handle in a loop, grepping `markerIndex` for 158930.
- Expected: a way to go from a link's `marker=N` (a marker index) to the marker itself, e.g. `profiler-cli marker info --index 158930` or `marker info` accepting the link, or `markerIndex` in the `--list --json` rows.
- Got: `--list --json` rows carry only the session handle; the index is only in `marker info`, one handle at a time. Checking a report's link whose index differs by one from the marker I found (a `Runnable` next to the `CCSlice` it named) took a shell loop over every marker in the window.
- What the default output could have shown: the marker index in `thread markers --list` (at least in `--json`), or a lookup by index.
