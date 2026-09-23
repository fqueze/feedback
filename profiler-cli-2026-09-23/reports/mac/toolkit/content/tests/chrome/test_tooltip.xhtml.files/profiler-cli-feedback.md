## Question: the precise order of events a few ms apart

- Question: in which order did a native mouse event and the test's synthesized mouse events reach the document, within ~10 ms?
- Command: `profiler-cli thread markers --session <s> --search 'DOMEvent,DispatchSynth,-eventType:MozAfterPaint' --list --limit 0`
- Got: every row's time is `t=7m47s` (rounded to the second), so rows 2 ms apart cannot be ordered or measured from the list; the list is sorted by start, but gaps are invisible.
- Workaround: a loop of `marker info <m> --json` per handle, printing `start` and the `eventType`/`target`/`latency` fields.
- What would have answered it: `--list` showing times at ms resolution when the view is short (or a `--precise-times` flag), plus the DOMEvent latency column.

## Question: where was a window on screen at time t (review-test_tooltip.xhtml)

- Question: was the test window still at the bottom of the screen when a later step failed?
- Command: `profiler-cli screenshots --at 467.247 -o <dir> --session review-test_tooltip.xhtml-1`
- Got: one image per window, of its content only (`win 69 700px × 700px`); nothing says where the window was on screen, so a window left moved cannot be seen. Had to fall back to counting `MozUpdateWindowPos` DOMEvents.
- What would have answered it: the window's screen position (and the screen size) with each screenshot row, as `CompositorScreenshot` knows the window it came from.

## Same question as above, again: ordering events a few ms apart

- Command: `profiler-cli thread markers --session <s> --search 'DOMEvent,DispatchSynth,-eventType:MozAfterPaint' --list --limit 0` in a 25 ms zoom
- Got: `t=5m7s` on every row again; needed a script over `--json` for `start`, `innerWindowID` and `latency` to check timestamps quoted to 0.1 ms.
