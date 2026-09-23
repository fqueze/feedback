## Marker times past one minute are rounded to the minute in text output

- Command: `profiler-cli thread markers --category Test --search browser_source_map-01.js --list --limit 0 --session browser_source_map-01.js-1`
- Expected: times precise enough to order and space the test's log lines (e.g. `t=59.951s`, `t=1m10.197s`).
- Got: every marker between 60 s and 120 s shows `t=1m`, the next one `t=1m10s`, the failure `t=2m30s`; `marker info` also says `Time: 1m`. The gap between "Adding a new tab" and the next line cannot be read.
- Workaround: `--json` and read `flatMarkers[].start`.

## IPC markers in `--list` show only "IPCIn"/"IPCOut", not the message type

- Command: `profiler-cli thread markers --search "name:IPCIn" --list --limit 0 --session browser_source_map-01.js-1` (thread t-26)
- Question: which IPC messages did this content process receive, in order? (Did it ever get `PContent::Msg_AddLoadedOrigin`?)
- Expected: each row labelled with its `messageType`, as the Task/Runnable markers are.
- Got: every row reads `IPCIn`; the type is only in `marker info` or the JSON `data.messageType`.
- Workaround: `--json` and a script printing `data.messageType`.
