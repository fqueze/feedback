## Question: "which IPC message is each of these IPCIn/IPCOut markers, in time order?"

- Command: `profiler-cli thread markers --search PCache --list --limit 0 --session <s>` on a content-process GeckoMain thread.
- Expected: each IPC row to name its message (`PCache::Msg_PCacheOpConstructor`, `PCacheOp::Msg___delete__`, ...), as Runnable rows already do in their description column.
- Got: `m-344  IPCOut  t=34.654s  1.456ms  ✗` with an empty description, so the list matched on the message type but did not show it. Pairing requests with replies (the whole diagnosis) was impossible from the list.
- Workaround: `--list --json | jq .flatMarkers[].handle`, then one `marker info <h> --json | jq '.fields[] | select(.key=="messageType")'` per handle, in a shell loop (20-450 calls per thread).
- What could have shown it: the `messageType` (and direction) in the description column of IPC rows in `--list`.

## Question (review): "which request does this IPC reply answer?" (added by review-browser_storage_cache_navigation.js)

- Command: `profiler-cli thread markers --search name:IPC --list --limit 0 --json --session <s>` on a content GeckoMain, then jq over `.data.messageSeqno`, `.data.sendStartTime`, `.data.recvEndTime`.
- Expected: some way to pair a `PCacheOp::Msg___delete__` reply with the `PCache::Msg_PCacheOpConstructor` that it answers (actor/routing id), or at least the send and receive times, each on its own process's side, in `--list`.
- Got: only the marker start. On Windows that start mixes the two processes' clocks, which are ~0.7 ms apart (IPCOut durations come out negative), so a reply seems to precede its request. I had to pair them from the parent-side `recvEndTime` of the request and `sendStartTime` of the reply, both of which are only in the JSON.
- What could have shown it: the routing/actor id on IPC markers, and send/recv times on each side in the `--list` row.
