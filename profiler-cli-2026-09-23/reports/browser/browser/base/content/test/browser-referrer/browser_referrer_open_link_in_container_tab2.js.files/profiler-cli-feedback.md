## IPC markers in `thread markers --list` carry no message type
- Question: "when did the parent send `PBrowser::Msg_UpdateDimensions` to content process X, relative to `Msg_Show`?"
- Command: `profiler-cli thread markers --session browser-container_tab2-1 --search "Msg_Show,Msg_UpdateDimensions" --list --limit 0`
- Expected: each IPCOut/IPCIn row showing its message type and peer (e.g. `PBrowser::Msg_UpdateDimensions → https://example.com (4/4)`), since the search matched on exactly that.
- Got: rows like `m-1719  IPCOut  t=3.185s  4.001ms  ✗` with an empty description, indistinguishable from the dozens of other IPC rows.
- Workaround: `marker info m-...` one by one, or `--json` piped to a script reading `fields[messageType]` and `otherPid`.

## Marker search does not match the text the list prints for DOMEvent markers
- Command: `profiler-cli thread markers --session browser-container_tab2-2 --search "contextmenu - document" --list --limit 0` (also `"contextmenu - "`)
- Expected: the `DOMEvent  contextmenu - document` row the list prints for that thread.
- Got: 0 matches; `--search contextmenu` finds it. The displayed label is composed from eventType and target, but search only matches the individual fields.
- Workaround: search on the event type alone and grep the output.

## (review) "Is this handle the marker a link's `marker=N` points to?" needs `--json`
- Command: `profiler-cli marker info --session browser-review-oplict2-1 m-213` (and 40-odd others, to check a report's links)
- Expected: the marker index in the text output, since a profiler link's `marker=N` is that index and handles are not.
- Got: name, time, and fields, but no index. Only `--json` has `markerIndex`.
- Workaround: `marker info <m> --json | python3 -c '...markerIndex...'`, once per marker. It would help to print `Index: N` in the text output, or to take `--index N` so the link can be opened directly.

## (review) "How regular are the vsyncs?" needs a script
- Command: `profiler-cli thread markers --session browser-review-oplict2-2 --search "messageType:PVsyncBridge::Msg_NotifyVsync" --list --limit 0` on the GPU Compositor
- Question: the median gap between successive markers of one kind, and how many gaps are about twice as long.
- Got: 189 rows at ms precision. They are two `IPCIn` markers per vsync, 0.3 ms apart, with nothing to tell them apart in the list, so a naive count gives a median of 0.3 ms.
- Workaround: `--json`, sort by start, merge markers less than 2 ms apart, compute the gaps. A `--gaps` or interval-stats option on `thread markers` would answer this directly.
