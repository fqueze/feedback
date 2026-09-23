## Question: "which IPC request never got its reply?" (test_closing_connections.html)

- Command: `profiler-cli thread markers --search messageType:PRemoteDecoder --list --limit 0 --session <s>`
- Expected: each IPC row to show its message type (and ideally seqno), so a `Msg_Decode` without a matching `Reply_Decode` can be spotted.
- Got: rows only say `IPCOut` / `IPCIn` with a duration; the message type, seqno and direction are only in `--json` `data`.
- Workaround: dumped `--json` and paired `Msg_Decode`/`Reply_Decode` by `data.messageSeqno` in a Python script (found 1338 requests, 1337 replies, one unanswered).
- What the output could have shown: the message type in the list row, and a way to list requests with no reply (e.g. `--unreplied`), or per-request round-trip latency.

## Question: "the sequence of IPC message types over time"

- Command: same as above, with `--group-by field:messageType` gives counts but loses order; `--list` keeps order but hides the type.
- Workaround: `--json` + script that run-length-encodes consecutive message types.
- What the output could have shown: `--list` rows labelled with `messageType` for IPC markers.
