## Which IPC message is an IPCIn/IPCOut marker?

- Question: which IPC messages (e.g. `PRemoteWorkerDebuggerManager::Msg_Register`) did a content process main thread send, and when.
- Command: `profiler-cli thread markers --session <s> --search RemoteWorkerDebugger --list --limit 0` (and `profile markers --search RemoteWorkerDebugger`).
- Expected: each IPCIn/IPCOut row to show its `messageType` (and direction), as the Runnable rows show their names.
- Got: IPCIn/IPCOut rows with an empty description column (`m-3614 IPCOut t=8.101s 0s ✗`), so the list could not say which message each was; the search matched on the hidden payload.
- Workaround: `--search "messageType:PRemoteWorkerDebuggerManager,..." --list --json` piped through a script printing `data.messageType`/`data.direction`. The default output could have shown `messageType` in the description column.

## Negative IPC durations hide a cross-process clock offset

- Question: in which order did a parent SendQuery and a child ReceiveQuery happen?
- Command: `thread markers --list` on the parent and on a content process.
- Got: child ReceiveQuery timestamps ~7 ms *before* the parent's matching SendQuery; the only hint was IPCIn markers with durations like `-6,987,700ns`.
- Could show: a per-process clock-offset note in `profile info`, or flag negative IPC durations as clock skew.

## (review) Which of two cross-thread events came first, below 1 ms?

- Question: did the content main thread's `ExecWorker` runnable end before the DOM Worker thread's `WorkerThreadPrimaryRunnable` had run for ~1 ms, and was `Msg_RegisterDone` before or after the `ExecWorker` end?
- Command: `profiler-cli thread markers --session <s> --search "ExecWorker,DispatchLockHeld" --list --limit 0` on the main thread, and the same on the worker thread.
- Expected: start and end times precise enough to order events 0.1 ms apart.
- Got: `t=34.694s  4.016ms` and `t=34.698s`: start rounded to 1 ms, no end column, so the order could not be read.
- Workaround: `--list --json`, printing `start` and `start + duration` from `flatMarkers`. The list could print ms with 0.1 ms precision inside a zoom narrower than a second, or an end column for interval markers.
