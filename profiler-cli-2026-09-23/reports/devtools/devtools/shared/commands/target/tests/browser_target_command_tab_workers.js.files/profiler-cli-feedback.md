## IPC markers in `--list` show no message type
- Command: `profiler-cli thread markers --session <s> --search RemoteWorkerDebugger --list --limit 0` (and `profile markers --search RemoteWorkerDebugger`)
- Expected: the description column of each IPCOut/IPCIn row to show the message type (e.g. `PRemoteWorkerDebuggerManager::Msg_Register`), since that field is what the search matched.
- Got: IPCOut/IPCIn rows with an empty description. Only `marker info m-N` showed `Type: PRemoteWorkerDebuggerManager::Msg_Register`.
- Workaround: ran `marker info` on each IPC handle to confirm which message it was.

## Question: when did this thread fall asleep and wake up, next to another thread's markers? (review)
- Command: `profiler-cli thread markers --session <s> --thread t-12 --search name:Awake --list --limit 0` after `zoom push 4.405,4.43`, the same for the worker thread, then `--json | jq '{start, end: (.start + .duration)}'`.
- The list shows `t=4.411s` (rounded to the ms) and a duration. The question needed each sleep gap (end of one `Awake` to start of the next) at µs precision, compared with the other thread's `Runnable` starts. Here the gaps were 0.1–1 ms apart.
- Could show: a sleep/wake view for one or more threads over a zoom, listing sleep intervals with µs start/end, interleaved with the other threads' markers.
