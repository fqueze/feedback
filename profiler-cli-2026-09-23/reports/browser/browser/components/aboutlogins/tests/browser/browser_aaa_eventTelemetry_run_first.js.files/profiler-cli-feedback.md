## Question: which IPC messages did the parent exchange with content process X around time T?

- Command: `profiler-cli thread markers --session S --search 1507 --list --limit 0` (on the parent main thread, zoomed to 2.076,2.100)
- Expected: each IPCIn/IPCOut row showing its message type (e.g. `PProcessHangMonitor::Msg_RequestContentJSInterrupt`, `PBrowser::Msg_Destroy`, `PContent::Msg_ScriptError`), like Runnable rows show their runnable name.
- Got: rows with only `IPCOut  t=2.080s  3.866ms` and no message type, direction or other process; the list is unreadable for IPC.
- Workaround: `--json` and a python script printing `data.messageType`, `data.direction`, `data.phase`, `data.otherPid`. Note also that some `flatMarkers` entries have no `fields` key (KeyError when scripting over `fields`), so the script had to read `data` instead.
- What the output could have shown: `IPCOut  t=2.083s  PProcessHangMonitor::Msg_RequestContentJSInterrupt -> https://example.com (pid 1507)`.

## Minor: `marker info --json` records carry no `handle`

- Command: `profiler-cli marker info m-1765 m-1766 m-1333 --session S --json`
- Expected: each record to say which handle it is, to match them back to the request.
- Got: `handle` absent (None); had to rely on order.

## Question (review): did the child get the interrupt request before or after the module evaluation began, and before the parent's IPCOut?

- Command: `profiler-cli thread markers --session S --list --limit 0` on the child main thread, zoomed to 2.0795,2.090; `marker info m-310 m-227` for the same.
- Expected: timestamps precise enough to order events across threads that are tens of microseconds apart (here `IPCIn RequestContentJSInterrupt` 2.08298, `ModuleEvaluation` 2.08376, `JS::InterruptCallback` 2.08386).
- Got: every row printed as `t=2.083s`/`t=2.084s`, and `marker info` prints `Time: 2.084s`; the ordering across the parent and child threads cannot be read. Also the same IPC rows with no message type as logged above, again needing a script.
- Workaround: `--json` and a python script printing `start/1000` with 5 decimals and `data.messageType`.
- What the output could have shown: the time with as many decimals as the zoom's span needs (sub-ms when the zoom is a few ms wide).
