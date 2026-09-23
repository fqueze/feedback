## Question: in what order did markers within the same millisecond happen (review-browser_toolbarKeyNav.js)

- Command: `profiler-cli zoom push 10.827,10.8345 --session S` then `profiler-cli thread markers --session S --list --limit 0`
- Expected: start times precise enough to order a runnable being queued (`TaskController::AddTask`), an `IPCIn` arriving and the runnable running, all within 100 µs.
- Got: about 40 rows all printed as `t=10.828s`. Durations have µs precision but start times do not, so the race could not be read.
- Workaround: `--json` and printed `flatMarkers[].start` with 4 decimals.
- What the output could show: when the view is a few ms wide, print starts in ms with µs (`t=10828.073ms`).

## Question: when did an IPC message reach the receiving process, as opposed to when it was handled

- Command: `profiler-cli marker info m-125 --session S` (an `IPCIn PBrowser::Msg_OnStateChange`)
- Expected: send, I/O-thread send, I/O-thread receive and handling times, on the same time base as the `Time:` line.
- Got: `startTime`, `sendStartTime`, `sendEndTime`, `recvEndTime` and `endTime` under "Other payload fields (no schema)", all raw. They are offset by 7.409 ms from the displayed times in DCawW8Ii, 8.106 ms in DtZsLA7J and 44.78 ms in OAdKPMzL, so comparing `recvEndTime` with other markers meant working out that offset from `endTime` against the displayed end.
- What the output could show: the IPC phases as labelled fields in the profile's own `t=` base.
