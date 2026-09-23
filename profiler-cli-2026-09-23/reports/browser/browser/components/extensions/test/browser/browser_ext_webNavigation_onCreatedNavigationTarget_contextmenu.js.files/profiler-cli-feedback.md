## Negative IPC durations in grouped output

- Command: `profiler-cli thread markers --session browser-ctxmenu-2 --search PVsync --group-by field:otherPid` (zoomed on a TestUtils marker), Windows profile of task Gtakuf_lSqOaLoIuoCp9HA.
- Expected: non-negative durations for IPCOut markers.
- Got: `9388: 313 markers (avg=-91,200ns, max=156.79μs)`, `8548: 19 markers (avg=-1,117,427ns, max=-1,108,217ns)`. Also, negative durations print in raw ns with thousands separators rather than μs/ms like positive ones. Cost little; the counts were what I needed.

## Review: "did this process ever stop observing vsync?" needed a script (review agent)

- Question: after t=6.5 s, which `PVsync::Msg_Observe` / `Msg_Unobserve` did the WebExtensions process send, and was the last one an Observe?
- Command: `profiler-cli thread markers --session browser-review-ctxmenu-1 --search "messageType:PVsync::Msg_Observe,messageType:PVsync::Msg_Unobserve" --list --limit 0 --json`, then Python filtering on `sendThreadName` containing `WebExtensions` and name `IPCIn`.
- Expected: a way to filter on two payload fields at once (AND), e.g. `messageType:...` plus `sendThreadName:WebExtensions`; comma is OR, so the default output mixed every process's messages (hundreds of rows).
- What would have answered it: an AND combinator for `field:value` terms, or `--group-by field:sendThreadName,field:messageType` showing first/last timestamp per group.

## Review: gaps in a periodic marker series needed a script (review agent)

- Question: does `RefreshDriverTick waiting for paint` (about 60 Hz) ever stop for more than 40 ms, and where?
- Command: `profiler-cli thread markers --session browser-review-ctxmenu-2 --search "name:RefreshDriverTick waiting for paint" --list --limit 0 --json`, then Python over the start times.
- The Frequency Analysis line gives only min/avg/max interval (max=3.682s), not where the long intervals are. Listing the N longest intervals with their start times would have answered it.
