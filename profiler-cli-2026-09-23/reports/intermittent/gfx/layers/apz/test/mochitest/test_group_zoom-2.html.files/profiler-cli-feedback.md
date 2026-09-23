## `thread markers --list` prints start times rounded to the second in long profiles

- Command: `profiler-cli thread markers --session test_group_zoom-2.html-2 --search NotifyObservers --list --limit 0` (a 11m54s profile; same in the 38m resource-usage profile)
- Expected: a start time precise enough to order markers: here everything that matters (GPU kill, compositor reinit, 14 touch events, content responses) happens within 300 ms.
- Got: `t=5m32s` on every row, so the chronology of hundreds of markers within one second is lost, and the list is not even sorted by start within that second.
- Workaround: `--json` and a script printing `flatMarkers[].start` in ms, sorted.

## Question: "which IPC message is this IPCIn/IPCOut marker?" needs one `marker info` per marker

- Command: `profiler-cli thread markers --search IPC --list` shows IPCIn/IPCOut rows with an empty label; the message type (`PAPZ::Msg_NotifyAPZStateChange`, `PAPZCTreeManager::Msg_SetTargetAPZC`...) and the other pid only appear in `marker info`.
- Needed: the list of IPC messages a thread sent/received in a window, with type, direction and other process, to see that no `PAPZ::Msg_NotifyAPZStateChange` reached the content process after the GPU restart.
- Workaround: `marker info <all handles> --json` and a script extracting `fields.messageType`, `rawFields.direction`, `fields.otherPid`. The list output could show `messageType` and direction/other pid as the label of IPC markers.

