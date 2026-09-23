## IPC markers in `--list` output show no message type

- Question: "when did the content process send the LS checkpoint for setItem?"
- Command: `profiler-cli profile markers --session <s> --search "Msg_AsyncCheckpoint,Msg_AsyncFinish" --limit 0`
  (same with `thread markers --search PBackgroundLS --list`)
- Expected: each row to name the IPC message, e.g. `IPCOut PBackgroundLSSnapshot::Msg_AsyncCheckpoint`.
- Got: rows read only `IPCOut` / `IPCIn`, with an empty description. The message type is shown only by `marker info m-N`, one marker at a time.
- Workaround: `marker info` on each handle. The list could show the `messageType` field as the description.
