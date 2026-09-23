# profiler-cli feedback (browser_screenshots_drag_scroll_test.js)

## Question: which of several markers in the same millisecond came first?

- Command: `profiler-cli thread markers --search "DeferredTask,SynthesizeMouse,eventType:pointerdown,requestAnimationFrame" --list --limit 0 --session <s>`
- Expected: enough time resolution to order a DeferredTask run, a ReceiveQuery, a pointerdown and a rAF callback that all happen within 2.5 ms of each other.
- Got: every row shows `t=44.282s` / `t=44.284s` (1 ms resolution), so the order that decides the race could not be read from the list; rows are sorted but ties at the printed precision look simultaneous.
- Workaround: `profiler-cli marker info m-N --json` per marker and read `start` (µs precision). The list could print sub-ms start times when a zoom is narrower than ~1 s, or offer `--time-precision`.

## `--search` does not match the displayed marker label

- Command: `profiler-cli thread markers --search "scroll - document" --list`
- Expected: the DOMEvent rows displayed as `scroll - document`.
- Got: nothing (the label is composed from `eventType` and `target`, and the search matches fields separately).
- Workaround: `--search eventType:scroll`. A hint in the no-match message ("try field:value; the label is built from fields eventType, target") would have saved a round trip.

## Question (review): which marker does a link's `marker=N` point to?

- Command: `profiler-cli load "<profiler.firefox.com link with marker=8543&thread=9>" --session <s>`, then `profiler-cli thread markers --list --limit 0 --json --session <s>`
- Expected: the load to report the marker the link selects (name, time, handle), or `flatMarkers[]` to carry `markerIndex` so N can be looked up.
- Got: the load prints only the selected thread; `flatMarkers[]` has `handle`, `name`, `start`, … but no `markerIndex`. A link whose text said "scroll" matched neither of the two scroll markers I tried first (7781 at 5.134 s); it was the second one (8543 at 5.760 s).
- Workaround: `marker info <candidate handles> --json` one by one until `markerIndex` equals N. `marker info --index N`, or printing the link's marker on load, would answer it in one call.
