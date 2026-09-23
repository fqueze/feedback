## Ordering markers within the same millisecond (question: "did the LayerTransforms update land between these two mousemoves?")

- Command: `profiler-cli thread markers --list --limit 0 --session browser_tooltip.js-1` (zoomed to 2.700,2.780)
- Expected: start times precise enough to order markers a fraction of a millisecond apart.
- Got: `t=2.755s` for the mousemove on the toolbar, the three `PAPZ::Msg_LayerTransforms` runnables and the next `PAPZInputBridge::Msg_ReceiveMouseInputEvent`, all printed with 1 ms resolution, so their order could not be read.
- Workaround: `marker info m-262 m-63 m-910 ... --json` and a python one-liner printing `start`/`end`, which gave 2754.68 / 2755.08 / 2755.27 / 2755.76. The list could print sub-ms times (e.g. `t=2755.08ms`) when the zoom range is short, or on demand.

## Matching a DOMEvent to the input event that caused it (question: "which of the two synthesized mousemoves is this DOMEvent?")

- Command: `profiler-cli thread markers --list --limit 0 --session browser_tooltip.js-3` on the content thread
- Expected: something in the row to tell which of two queued `PBrowser::Msg_DispatchMouseEvent` sends a `DOMEvent mousemove` came from.
- Got: only `DOMEvent  mousemove - h1@...` and its duration; the `latency` field (dispatch time minus the event's timestamp), which answers it, is only in `marker info --json`.
- Workaround: `marker info m-67 m-139 --json`, subtract `latency` from `start`, compare with the sends' start times. Showing DOMEvent latency in the list row would have answered it directly.

## `thread markers --list --json` rows have no `end`

- Command: `profiler-cli thread markers --list --limit 0 --json --session browser_tooltip.js-3`
- Expected: `start` and `end` (or `duration`) per flat marker, as `marker info --json` has.
- Got: `end` absent, so a script computing durations printed 0 for every marker.
- Workaround: none needed for this question, only start times were used.
