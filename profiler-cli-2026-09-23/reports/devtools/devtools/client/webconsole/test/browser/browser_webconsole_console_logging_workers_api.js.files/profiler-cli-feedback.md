## Which of two markers on different threads came first, when they are less than 1 ms apart

- Command: `profiler-cli thread markers --list` and `profiler-cli marker info m-1609` (text output).
- Expected: start and end times precise enough to order a content main-thread `ScriptExecution` against the worker's `WorkerThreadPrimaryRunnable` and a `WorkerPrivate::DispatchLockHeld` in between (all within 0.5 ms).
- Got: `t=4.103s` for all three, and `Time: 4.103s - 4.103s` in `marker info`.
- Workaround: `marker info <m> --json`, reading `start` and `end` in ms. The text output could print more digits when the duration is below 1 ms, or print the start in ms.

## What an IPC marker's message type is, in a flat list

- Command: `profiler-cli thread markers --search IPC --list` on a content main thread.
- Expected: the IPC message type (e.g. `PRemoteWorkerDebuggerManager::Msg_Register`) in the row.
- Got: bare `IPCIn` / `IPCOut` rows with an empty description, so there is no way to find one message in the list. `--group-by field:messageType` and `marker info` do show it.
- Workaround: `profile markers --search <message type>`, which matches the payload.
