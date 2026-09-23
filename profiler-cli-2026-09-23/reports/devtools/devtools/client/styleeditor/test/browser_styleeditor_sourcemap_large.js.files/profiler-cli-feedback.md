## Question: how long before the TEST-UNEXPECTED-FAIL did the click start, and did anything happen in between?

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_styleeditor_sourcemap_large.js --list --limit 0`
- Expected: timestamps precise enough to order markers within one second.
- Got: every marker printed as `t=1m19s` (the click, the failure, the following passes all read the same). I needed `--json` and `.flatMarkers[].start` to get 79136.8 ms (click) vs 79199.9 ms (failure).
- Suggestion: print milliseconds (`t=1m19.200s`) in `--list`, as is already done below one minute (`t=8.792s`).

## Default thread after `load` was a WebExtensions thread

- Command: `profiler-cli load <.../profile_browser_styleeditor_sourcemap_large-2.js.json> --session <s>`
- Expected: selected thread = parent process GeckoMain (as happened for the first profile, t-0).
- Got: `Selected thread: t-18 (GeckoMain, WebExtensions)`; my next `thread markers` returned "No markers match" until I ran `thread select t-0`.

## Question: which marker index is behind this handle? (review-browser_styleeditor_sourcemap_large.js)

- Command: `profiler-cli marker info m-12 --session <s>`
- Expected: the marker's index, to check that a report's link `marker=N` points at the quoted marker.
- Got: default output shows type, time, fields, but no index; needed `marker info m-12 --json` piped through a script to read `markerIndex`.
- Suggestion: print `Index: N` (or the profiler link) in the default `marker info` output.
