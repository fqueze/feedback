## IPC message type missing from default output

- Command: `profiler-cli thread markers --session <s> --search "type:IPC" --list --limit 0` and `profiler-cli marker info m-776 --session <s>`
- Question: which IPC messages (e.g. `PNecko::Msg_PHttpChannelConstructor`, `PHttpBackgroundChannel::Msg_OnProgress`) did the parent send/receive around a request?
- Expected: the list rows and `marker info` show `messageType` (it is the one field that identifies an IPC marker).
- Got: list rows show only `IPCIn`/`IPCOut` with an empty description; `marker info` text prints timing fields and direction but not `messageType`, `sendThreadName` or `recvThreadName`, which are only in `--json` (`fields[]`).
- Workaround: `--list --json` piped through a Python script printing `data.messageType` and `data.niceDirection`.
