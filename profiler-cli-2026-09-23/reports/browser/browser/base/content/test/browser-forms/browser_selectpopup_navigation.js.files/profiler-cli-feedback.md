## IPC markers in `thread markers --list` show no message type

- Command: `profiler-cli thread markers --search DOMEvent,IPCIn,IPCOut --list --limit 0 --session <s>`
- Question: in which order did the content process receive and handle `PContent::Msg_UpdateSharedData` and `PBrowser::Msg_RealMouseButtonEvent`?
- Expected: each `IPCIn`/`IPCOut` row to carry its message type (`PBrowser::Msg_RealMouseButtonEvent`, seq), like `Runnable` rows carry theirs.
- Got: rows with an empty description (`m-272  IPCIn  t=33.589s  17.451ms  ✗`), and ~50 identical-looking IPCOut rows; I had to `marker info` a range of handles to find which was which.
- Workaround: `marker info m-271..m-275`, or search `Runnable` markers, whose names include the message type and priority.

## (review) `marker info --json` changes shape with the number of handles

- Command: `profiler-cli marker info m-11 m-14 m-12 --json --session <s>` vs `profiler-cli marker info m-11 --json --session <s>`
- Question: the message type, seqno and `markerIndex` of several IPC markers at once.
- Expected: the same record shape either way (a list, or always `{markers: [...]}`).
- Got: one handle returns the marker object itself; several return `{type, requested, markers, errors, context}`. A script written against one breaks on the other (`KeyError: 'fields'`).
- Workaround: read `["markers"]` when several handles are given.

## (review) IPC raw time fields are in another time base than the displayed times, unsaid

- Command: `profiler-cli marker info m-26 --session <s>` (an `IPCIn`)
- Question: when did the content process receive and handle this message, relative to the other markers?
- Expected: `recvEndTime`/`endTime` in the same timeline as `Time:`, or labelled as not.
- Got: `Time: 33.589s - 33.606s`, but `startTime: 33596.71`, `endTime: 33614.16`, `recvEndTime: 33597.47` under "Other payload fields": about 7.2 ms later than the displayed timeline, with nothing saying so. The report under review quoted them as "(raw)" next to displayed times.
- Workaround: subtract the offset between `startTime` and the displayed start.
