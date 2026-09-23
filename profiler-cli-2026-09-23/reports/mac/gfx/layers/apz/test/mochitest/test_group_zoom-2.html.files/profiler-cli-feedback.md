## Question: which IPC messages did this thread send or receive, to which process, in this window?

- Command: `profiler-cli thread markers --session <s> --search IPC --list --limit 0` (GPU process Compositor thread, a 140 ms window)
- Expected: one row per message with its type and peer, e.g. `IPCOut PAPZ::Msg_NotifyAPZStateChange -> pid 4831`.
- Got: every row reads `IPCIn IPCIn` or `IPCOut IPCOut`. The message type, direction and peer pid are only in the payload.
- Workaround: `--json`, then a script printing `data.messageType`, `data.direction`, `data.otherPid`, keeping `data.phase == "endpoint"`. The whole diagnosis (which process APZ talked to during the pinch, pass versus fail) rested on that script.
- What the default output could show: the `messageType` and the other side's pid (or process name) in the label of IPC markers.

## Question: in what order, and how far apart, did these log lines come?

- Command: `profiler-cli thread markers --session <s> --search test_group_zoom-2 --list --limit 0` on a 38-minute resource-usage profile
- Expected: timestamps precise enough to order the lines and see a gap of tens of milliseconds.
- Got: every row between 925 s and 927 s prints as `t=15m25s` or `t=15m27s`, so the order within a second and the gaps are invisible.
- Workaround: `--json` and `start / 1000` rounded to the millisecond.
- What the default output could show: seconds with milliseconds (`t=927.096s`) on long profiles too, as short profiles already do.
