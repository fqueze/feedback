## Question: which IPC messages did content process X exchange with the parent in this window, in order?

- Command: `profiler-cli thread markers --session <s> --thread t-0 --search "Msg_" --list --limit 0` (zoomed to 31 ms)
- Expected: each IPCIn/IPCOut row to show its message type and peer (e.g. `PWindowGlobal::Msg_SetSingleChannelId ← Web Content (5/13) pid 9896`).
- Got: IPCIn/IPCOut rows with an empty description (only name, time, duration), so the message type and the other process were invisible; only the `Runnable` rows name the message, and they do not say which process sent it.
- Workaround: `--search "otherPid:9896,otherPid:6052" --list --limit 0 --json` piped through a Python script printing `fields.messageType` and `fields.otherPid`. Needed for every IPC ordering question in this investigation.
- Also: IPC markers with a long duration (6 s) that merely overlap the zoom range are listed first in every zoomed list, burying the ones that start inside it.

## `marker info --json` has no handle

- Command: `profiler-cli marker info m-1749 m-3289 --session <s> --json`
- Expected: each record to carry its `m-N` handle, to match records to the handles asked for.
- Got: `handle` is absent (None) in each record; had to rely on the order of the arguments.

## `thread markers --search` does not match the text it prints for Counter markers (review)

- Command: `profiler-cli thread markers --session <s> --search "bfcache.combo" --list --limit 0` (also `--search "Req: 1"`)
- Expected: the `Counter::add` rows the same list prints as `bfcache.combo Req: 1` / `bfcache.combo BFCache_Success: 1`.
- Got: "No markers match"; `--search combo` or `--search bfcache` finds all 12. The printed description is assembled from separate fields (category, name, label, value), so a search for the printed text silently misses.
- Workaround: search one word of it.
