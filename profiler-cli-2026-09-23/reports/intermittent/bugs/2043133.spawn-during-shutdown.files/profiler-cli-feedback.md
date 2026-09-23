## Question: "in what order did the parent main thread handle these IPC messages from pid N, relative to its own markers?"

- Command: `profiler-cli thread markers --thread t-0 --list --limit 0 --session 2043133.spawn-during-shutdown-1` over a 20 ms zoom.
- Expected: one row per message, with type, direction, peer pid and seqno, placed at the time the main thread ran it, next to markers such as `nsHttpChannel::OnStartRequest` and `BFCache not cached`.
- Got: `IPCIn` rows placed at the start of an interval that begins in the sender's clock (negative durations are common). Sorting by that start put `PDocumentChannelConstructor` before `OnStartRequest`, although the main thread ran it 3 ms later. The only reliable handling time is the `Runnable` marker named after the message.
- Workaround: `--json` and a script printing `Runnable` markers whose label names a message, plus `data.messageType`/`data.messageSeqno`/`data.otherPid` of `IPCOut` markers.

## Question: "when did each process start and end?"

- Command: `profiler-cli thread list --session ...` and `profiler-cli profile info --all --session ...`
- Expected: each process's start and end time, as seconds.
- Got: `thread list` has no times; `profile info` prints `[ts-r → ts-u]` handles only. The seconds are only in `profile info --json` (`startTime`/`endTime`, in ms).
- Workaround: `profile info --all --json` and a script.
