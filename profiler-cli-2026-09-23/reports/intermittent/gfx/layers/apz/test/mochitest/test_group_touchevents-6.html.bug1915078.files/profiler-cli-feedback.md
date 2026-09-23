## Question: in what order, to the millisecond, did these DOM events / IPC messages happen within one second?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` on a 9-minute per-test profile (zoomed to a 2 s range).
- Expected: timestamps precise enough to order events a few ms apart (touchmove vs touchend vs scroll), e.g. `t=228.574s` or ms relative to the zoom start.
- Got: every row printed `t=3m48s` / `t=3m49s`: whole-second resolution, even inside a 2 s zoom. The diagnosis hinged on 1.5 ms gaps between two touchmoves.
- Workaround: `--json` and a Python script to print `flatMarkers[].start`. The list could print ms (or sub-ms) when the zoom range is short, or always print seconds with 3 decimals.

## Question: when was this IPC message received vs handled, on the same time base as the other markers?

- Command: `profiler-cli marker info m-167 --session <s>` (IPCIn `PBrowser::Msg_SynthesizeNativeTouchPoint`), then comparing with DOMEvent times from `thread markers --json`.
- Expected: the IPC's receive/handle times on the same base as marker `start`.
- Got: the default output shows `startTime: 228220.03 / endTime: 228575.22 / sendStartTime ...` as raw payload fields, which are on the profile's absolute base, while the marker's own `start` (and every other marker time) is relative to `rootRange.start` (10.24 ms earlier here). Comparing them made the content process appear to receive a touchend 5 ms before the parent had synthesized it.
- Workaround: subtract `context.rootRange.start` by hand. Showing the IPC phase times converted to the marker time base (or labelled as absolute) would avoid this.
