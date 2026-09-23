## `thread markers --list` rounds times to the second

Command: `profiler-cli thread markers --session <s> --category Test --search browser_net_domain-not-found --list --limit 0`

Expected: marker start times precise enough to order and diff the test's log (ms).
Got: 19 of the 20 markers printed as `t=2m58s`, though they span 177.804s to 178.9s; the question was "how long after the reload was each step logged, and what happened in between".
Workaround: `--json` and a script printing `flatMarkers[].start` in ms.


## `thread markers --list` does not show which IPC message an IPCIn/IPCOut row is

Question: which IPC messages did this content process send, and when (was `PContent::Msg_HistoryCommit` sent once or twice)?
Command: `profiler-cli thread markers --session <s> --thread t-77 --search History --category IPC --list --limit 0`
Expected: the message type on each row, as `Runnable` rows show it (`PContent::Msg_HistoryCommitIndexAndLength - ...`).
Got: rows labelled only `IPCOut` / `IPCIn`, with no message name. The `--json` `rawFields` do not carry it either. Only `marker info` shows `Type: PContent::Msg_HistoryCommit`, one marker at a time.
Workaround: `marker info` on each handle, or reading the matching `Runnable` rows on the receiving thread.
