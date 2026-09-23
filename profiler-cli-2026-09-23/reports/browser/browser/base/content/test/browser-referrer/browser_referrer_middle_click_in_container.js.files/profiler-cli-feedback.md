## Question: which IPC messages did this thread send/receive, and when?

- Command: `profiler-cli thread markers --search "messageType:PBrowser" --list --limit 0 --session browser-mcic-1` (thread t-17, a content process)
- Expected: one row per IPC marker with its message type (e.g. `PBrowser::Msg_UpdateDimensions`) and direction, the way DOMEvent rows show the event type and target.
- Got: rows reading only `IPCIn  t=4.012s  75.741ms` / `IPCOut ...` with an empty description, so the search matched the right markers but the list could not say which message each one was. `--group-by field:messageType` gives the types, but loses the chronology.
- Workaround: `--json` and a python script printing `data.messageType`, `data.direction` and `start` per flat marker. This was the key evidence (Show at 4.012s, UpdateDimensions at 4.145s, after the click at 4.120s).
- What would have answered it: show `messageType` (and sent/received + other thread) as the description of IPC markers in `--list`.
