## Ordering markers across threads within one millisecond
- Question: which ran first, the iframe's `ReceiveQuery [SpecialPowers] Spawn` or its `PBrowser::Msg_ChildToParentMatrix` handler? They are under 1 ms apart, on different threads from the parent.
- Command: `profiler-cli thread markers --list --limit 0 --search ... --session S`
- Expected: timestamps precise enough to order markers (e.g. t=9.2767s), at least on request (`--precision`).
- Got: `t=9.277s` for everything in that millisecond. I had to run `marker info m-A m-B ... --json` and print `start` myself.

## IPC markers in --list have no message type
- Question: which IPC message is each IPCIn/IPCOut row?
- Command: `profiler-cli thread markers --list --search name:IPCOut --session S`
- Expected: the `messageType` (e.g. `PBrowserBridge::Msg_UpdateDimensions`) in the description column, as the Runnable rows already give their task name.
- Got: an empty description. I needed `marker info` on each handle, 15 at a time, through a script.

## marker info --json: the output shape changes
- Command: `profiler-cli marker info m-63 m-64 m-70 m-73 m-78 m-79 --session S --json`, with handles from two threads (t-19, t-6).
- Expected: a list of records, as I got with several handles from one thread.
- Got: a single object, not a list, so my loop failed (`'str' object has no attribute 'get'`). Another multi-handle call returned records without `markerHandle`. Workaround: one `marker info` call per handle.

## (review) Can event times be compared across processes within 1 ms?
- Question: did the iframe process handle the query before the matrix message was even sent? That means comparing an IPCIn in one process with an IPCOut in another.
- Command: `profiler-cli marker info m-35 --session review-browser_test_zoom.js-2 --json`, and others like it.
- Got: many IPCOut markers have negative durations (`-317,235ns`). t-19 handles a transaction 1.1 ms before t-6's IPCOut sends it. Nothing says that process clocks are about 1 ms apart.
- Workaround: bounded the skew by hand from query/reply pairs, then fell back to orderings on a single thread.
- Could have shown: a note that a process's clock offset is uncertain, or each IPC phase (send, recv on I/O thread, recv on main thread) with the thread it came from.
