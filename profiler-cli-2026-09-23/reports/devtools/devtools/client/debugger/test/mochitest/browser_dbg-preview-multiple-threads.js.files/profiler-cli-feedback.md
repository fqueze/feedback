# profiler-cli feedback (browser_dbg-preview-multiple-threads.js)

## Question: in what order did these markers happen, within one millisecond, across threads?
- Command: `profiler-cli thread markers --session S --list --limit 0` (on a 5m20s profile)
- Expected: timestamps precise enough to order markers a fraction of a ms apart (worker constructor end vs worker thread start vs IPC send).
- Got: `t=4m30s` for every row; all markers of the 5 ms window print the same time.
- Workaround: `--list --json` piped to a Python script printing `start` in ms. The default output could print ms (or relative to the first row) when the listed markers span little time, or offer `--precise`.

## Question: which IPC message is this IPCIn/IPCOut marker?
- Command: `profiler-cli thread markers --session S --search "messageType:RemoteWorkerDebugger" --list --limit 0` and `profiler-cli profile markers --search RemoteWorkerDebugger`
- Expected: the row to say `IPCOut PRemoteWorkerDebuggerManager::Msg_Register -> pid 4012`.
- Got: rows labelled only `IPCIn` / `IPCOut`; the message type is only visible through `marker info` on each handle.
- Workaround: `marker info m-a m-b ... --json` and a script reading `fields[messageType]`.

## zoom push silently accepts a range in ms
- Command: `profiler-cli zoom push 270000,271500 --session S` (meant ms, the tool takes seconds)
- Expected: an error or warning, since the range lies entirely past the end of a 5m20s profile.
- Got: `View: ts>10O->ts>10p (25m)` and then "0 markers in view" on every query, with no hint why.
- Workaround: re-zoom in seconds.
