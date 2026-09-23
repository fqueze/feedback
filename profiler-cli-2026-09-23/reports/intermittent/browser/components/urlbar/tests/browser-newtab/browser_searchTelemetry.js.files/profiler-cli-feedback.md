## Question: in what order did these markers happen, within the same second?

- Command: `profiler-cli thread markers --search DOMEvent,ContentTaskUtils,TestUtils --list --limit 0 --session <s>` on a 2m4s profile, zoomed to a 250 ms window.
- Expected: timestamps precise enough to order markers that are milliseconds apart (the whole diagnosis was "did the view open before or after the helper's tryOnce").
- Got: every row printed `t=1m53s`; the default list only has second granularity once the profile is over a minute long, even inside a sub-second zoom.
- Workaround: `--json` and a Python one-liner printing `start` in ms. The list could print ms (or ms relative to the zoom start) when the zoom range is under a few seconds.

## Question: how long did each of these Runnables last?

- Command: `profiler-cli thread markers --search Runnable --min-duration 8 --list --json`, printing `end`.
- Expected: an end time for interval markers.
- Got: no `end` on the flat markers (only `start` and `duration`), so my first script printed end == start and I misread them as instants.
- Workaround: read `duration`. Minor; `end` alongside `start` would avoid it.

## Question: when was this IPC message sent, on the profile's timeline? (review)

- Command: `profiler-cli marker info m-438 --session review-browser_searchTelemetry.js-2` (IPCIn `PWindowGlobal::Msg_RawMessage` on t-45 of W9mIw0kHRKKvIpNULm0uVg, Windows content process).
- Expected: `sendStartTime` / `startTime` / `endTime` under "Other payload fields" on the same timeline as the marker's `Time` and the `thread markers` list.
- Got: the listed start is 109598.4, but the payload says `startTime: 109619.68` and `sendStartTime: 109619.69`, about 21 ms later. The listed times match the runnable that processed the message (start + duration = 109605.7 = the runnable's start), so the payload times are the process's raw clock, not shifted to the profile's zero. On a Linux profile the offset was only 0.24 ms, which hides the problem there.
- Workaround: use the listed start and duration only. The output could shift these payload timestamps like the marker's own, or flag them as unshifted.
