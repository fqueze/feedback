## Question: "in what order, to the millisecond, did these markers happen within one second?"

- Command: `profiler-cli thread markers --search "eventType:scroll,RefreshDriverTick,NotifyIntersectionObservers,..." --list --limit 0 --session <s>` on a profile 4m19s long.
- Expected: a start time per row precise enough to order and space events inside a 100 ms window (e.g. `t=258.2193s`), plus the duration.
- Got: every row printed `t=4m18s`. The durations are there, but the start time is rounded to the second, so which of two markers 30 ms apart came first, and how far apart they were, is not readable.
- Workaround: `--json` and a small script printing `start - base` in ms for each `flatMarkers[]` entry.
- Same for `screenshots --range`: rows print `t=4m18s`, so which screenshot is before/after an event needs `marker info --json` per screenshot.

## Question: "when was this timer set?"

- Command: `profiler-cli marker info m-177` (a `setTimeout callback` TextStack marker).
- Expected: the time at which the stack was captured (i.e. when setTimeout was called), since it differs from the marker start by the timer delay.
- Got: `Captured at: 4m18s` in the text output — second precision again, same second as the callback. `--json` has `stack.capturedAt: 258114.0` (135 ms before the callback), which is what answered it.

## Question (review): "are the times this report quotes the ones the profiler UI shows?"

- Command: `profiler-cli load '<profiler.firefox.com link with range=258200m110>' --session s`, then `profiler-cli marker info m-142 --json --session s`.
- Expected: `context.currentViewRange` in the same time base as the marker's `start`, and `thread markers --list` filtered to that range.
- Got: `currentViewRange.start` was 258207.94, which is the URL range plus `rootRange.start` (7.94 ms). But the listing covered marker `start` values 258200–258310, and "Scroll up" at `start` 258203.4 was inside the view. The two fields use different zeros, and nothing says which one the UI displays.
- Workaround: I reasoned from `profile-link.py`, which writes marker `start` straight into the URL, that `start` is zero-relative. That took several calls.
