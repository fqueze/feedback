## Marker times in `thread markers --list` are rounded to the second

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_history_recently_closed.js --list --limit 0`
- Question: in which order, to the millisecond, did the test's steps happen (click "restore all" at 124.42 s, wait resolved at 124.52 s, failure at 124.525 s)?
- Expected: timestamps precise enough to order events within one second (e.g. `t=124.421s`).
- Got: `t=2m4s` / `t=2m5s` on every row of a 2-minute profile, so a whole sub-test collapses onto two values.
- Workaround: `--json` piped to a python script printing `flatMarkers[].start`.

## No AND between marker name and payload in `--search`, across threads

- Command: `profiler-cli profile markers --session <s> --search "name:DllLoad,firefox\xul.dll"`
- Question: how long did each process take to load `xul.dll` (one row per process)?
- Expected: a way to require both the marker name `DllLoad` and the payload text `xul.dll`, with the duration and process on each row.
- Got: comma is OR, so it matched every DllLoad plus everything mentioning xul.dll (1800 markers across 35 threads); plain `--search xul.dll` still mixes in non-DllLoad markers and truncates.
- Workaround: `--limit 0 --json` piped to python filtering `name == "DllLoad"` and printing `processName`, `start`, `duration`. That showed 2 processes at 2.4-3.2 s against 4-7 ms for ~30 others.

## IPC rows in `thread markers --list` do not show the message type

- Command: `profiler-cli thread markers --session <s> --search "RestoreDocShellState,..." --list` (zoomed on 124.40-124.53 s)
- Question: which IPC message to which process took 2.4 s?
- Expected: `IPCOut PWindowGlobal::Msg_RestoreDocShellState -> https://example.net 2.404s` on the row, like Runnable rows show their name.
- Got: `m-622 IPCOut t=2m4s 2.404s` with an empty description; the message type and destination only appear in `marker info`.
- Workaround: `marker info m-622 m-626`, or `--json` and print `data.messageType`.
