## Question: which IPC messages crossed between two processes, in which order (type, direction, seqno)?

- Command: `profiler-cli thread markers --search "IPCIn,IPCOut" --list --limit 0 --session <s>` (also `profile markers --search "messageType:PContent::Msg_SetFocusedBrowsingContext"`)
- Expected: each IPC row to show its message type, the other side (thread/process) and the seqno, like the tooltip does (`IPC — sent to https://example.com (5/5)`), so a cross-process ordering race can be read from the list.
- Got: rows show only `IPCIn` / `IPCOut` and a duration, with an empty description. The `messageType:` search does select the right markers, but the row still does not say which message it is. Runnable rows do show `PContent::Msg_...` names, but not the sender, direction or seqno.
- Workaround: for each handle, ran `profiler-cli marker info m-N --json` in a shell loop, with python extracting `messageType`, `sendThreadName`, `recvThreadName` and `rawFields.messageSeqno`. That was about 150 calls for one 10 ms window, and it was the key evidence (a relay sent after the spawn on the same channel, seq 74 vs 75).
- What the output could have shown: `IPCOut  t=4.906135  PContent::Msg_SetFocusedBrowsingContext  → https://example.com (5/5)  seq 75` directly in the `--list` row.
