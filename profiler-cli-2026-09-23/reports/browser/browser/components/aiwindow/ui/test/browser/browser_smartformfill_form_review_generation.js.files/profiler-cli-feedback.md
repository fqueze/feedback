## Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --search "eventType:focus,eventType:scroll,..." --list --limit 0 --session <id>`
- Expected: a time column precise enough to order events a few ms apart (the race here was focus at 454830.6 ms vs the test's scroll at 454835.2 ms vs the scroll event at 454841.1 ms).
- Got: every row reads `t=7m35s`; `marker info` also prints only `Time: 7m35s (instant)`. Rows are not sorted by start time either (nested markers appear out of order).
- Workaround: `--json` piped to a python script printing `start` in ms and sorting by it.
- Could have shown: millisecond (or sub-ms) start times when the view is zoomed to a few seconds, or always in `--list` mode; and chronological sorting.
