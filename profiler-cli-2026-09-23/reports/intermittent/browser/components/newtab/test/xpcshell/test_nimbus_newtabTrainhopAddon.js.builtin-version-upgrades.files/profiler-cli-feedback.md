## Question: which IPC messages of one type were sent, when, and to which process

- Command: `profiler-cli thread markers --search 'messageType:PContent::Msg_RegisterChrome' --list --limit 0 --session <s>`
- Expected: one row per `PContent::Msg_RegisterChrome` send, with the message type and the recipient process in the row.
- Got: 852 rows all labelled only `IPCOut`, no message type, no recipient; the `messageType:` search is a substring match, so `Msg_RegisterChromeItem` rows are included too and cannot be told apart in the list.
- Workaround: `--json` and a Python filter on `data.messageType == ...` and `data.direction == 'sending'`, printing `start` and `data.recvThreadName`.
- What would have answered it: IPC rows in `--list` showing `messageType` and the other side (as the marker-table does), and an exact-match form for `field:value` searches.
