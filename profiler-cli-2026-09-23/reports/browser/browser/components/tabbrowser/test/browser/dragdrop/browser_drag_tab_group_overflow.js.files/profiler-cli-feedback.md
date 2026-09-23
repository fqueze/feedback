# profiler-cli feedback (browser_drag_tab_group_overflow.js)

## IPC markers in `--list` show no message type or peer

- Command: `profiler-cli thread markers --search FlushTabState --list --limit 0 --session S` (parent main thread, zoomed 14.4–15.1s)
- Expected: each IPCIn/IPCOut row names its message type (`PSessionStore::Msg_FlushTabState`), its direction, and its peer process, as `Runnable` rows name their task.
- Got: rows like `m-62  IPCOut  t=14.487s  23.611ms  ✗` with an empty description. You need `marker info` on each row, or `--json`, to learn which message and which process it was.
- Workaround: I first scripted over `--json` to answer "which content processes did the parent send `PSessionStore::Msg_FlushTabState` to, and which processes sent it any IPC at all". Later I found that `--group-by field:otherPid,field:messageType` answers most of it. That was not discoverable from the list output. Printing `messageType` and the peer process name in the list description would have saved several round trips.

## Zoom counts long IPC markers from outside the window

- Command: `zoom push 12.9,14.6`, then counting `PContent::Msg_ConstructBrowser` IPCOut markers.
- Expected: messages sent inside the window.
- Got: 68 instead of 17. Messages sent at 10.5s to a process that handled them only at 15.3s overlap the window, so they are included. That is correct for interval semantics, but it is a trap for IPC markers, whose interval runs from send to the recipient's dispatch. Nothing in the output says the count includes markers that started before the window.
- Workaround: filter on the start time with `--json`. A `--starts-in-range` option, or a note of how many markers started before the zoom, would answer it.

## Question needing a script: first occurrence per group

- Question: "when did each content process first send `PBrowser::Msg_PSessionStoreConstructor` after startup?"
- Command: `thread markers --category IPC --search "messageType:PBrowser::Msg_PSessionStoreConstructor,-otherPid:1908" --list --limit 0 --json`, then a script.
- `--group-by field:otherPid` gives counts and the longest examples, not the first and last start time per group. First and last time per group would have answered it directly.
