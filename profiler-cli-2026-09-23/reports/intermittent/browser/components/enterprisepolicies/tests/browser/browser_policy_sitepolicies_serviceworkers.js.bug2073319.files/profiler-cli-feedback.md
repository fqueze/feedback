## `--search` does not match a marker's label as printed

- Command: `profiler-cli thread markers --session <s> --search "bfcache.combo" --list --limit 0`
- Expected: the `Counter::add` markers that the same list prints as `bfcache.combo Req: 1` / `bfcache.combo BFCache_Success: 1`.
- Got: `0 markers`. The label is assembled from the `cat` and `id` payload fields, so the dotted string exists in no single field.
- Workaround: `--search combo` (or `id:combo`).

## Question: "when did the parent's I/O thread receive this IPC message?"

- Command: `profiler-cli thread markers --search "SetSingleChannelId,PDocumentChannelConstructor,OnStartRequest" --list --json`, then a script subtracting `context.rootRange.start` from each IPC marker's raw `recvEndTime`.
- Why: an `IPCIn` row's `t=` is the sender's send time, on the sender's clock. On Windows (task Xi07cAzER1u1GubTr8LfpA) the child's clock is several ms off, so the sorted list showed the child's `SetSingleChannelId` and `PDocumentChannelConstructor` arriving 3 ms *before* the `Navigation::Start` that triggered them. To order a race on the receiving thread, I needed the receive time in profile time.
- What the default output could show: the receive-side time (`recvEndTime`, rebased to profile time) next to `t=` for `IPCIn` rows, or a `--by recv` sort. `marker info` also prints the raw, un-rebased `startTime`/`recvEndTime`, which do not line up with the `t=` values it prints above them.

## (review) A marker with a negative duration drops out of a zoom that contains its start

- Command: on task Xi07cAzER1u1GubTr8LfpA (Windows), `profiler-cli zoom push 5.647,5.655` then `profiler-cli thread markers --list --limit 0 --search "LoadURI"` on the parent main thread.
- Expected: the parent's `IPCOut PContent::Msg_LoadURI`, whose row says `t=5.650s`, inside the range.
- Got: no match. The marker's end is 4 ms before its start (`-4,009,915ns`: the receiver's clock is behind the parent's), so its end, 5.646, falls before the range, and the overlap test drops it. With `zoom push 5.640,5.660` it shows.
- Workaround: widen the zoom by the clock skew. Could be: treat a marker as spanning min(start,end)..max(start,end) when filtering by range, or include it if its start is in range.
