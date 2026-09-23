## `thread markers --list` time column is whole seconds in a long profile, even when zoomed

- Command: `profiler-cli zoom push 103.50,104.07 --session S; profiler-cli thread markers --session S --search DOMEvent --list --limit 0` (per-test profile of 1m47s)
- Expected: a start time per row with ms precision, at least inside a 570 ms zoom, to order a click against touch events.
- Got: every row `t=1m44s`. The same for `marker info` (`Time: 1m44s - 1m44s`).
- Workaround: `--json` and read `start` myself (script over `flatMarkers`).
- Question the default output could not answer: "how many ms apart are these events, and in which order across two threads".


## (review-browser_touch_all_events_zoom.js) Same as above, hit again by the reviewer

- Command: `profiler-cli thread markers --search name:DOMEvent --list --limit 0 --session S` inside a 12 ms zoom, then `marker info m-1..m-28`.
- Got: every row `t=1m44s`; needed a script over `marker info --json` (`start`, and the `latency` field) to order 28 events 0.1 ms apart.
- Question: "in what order, and how far apart, did these events fire".
