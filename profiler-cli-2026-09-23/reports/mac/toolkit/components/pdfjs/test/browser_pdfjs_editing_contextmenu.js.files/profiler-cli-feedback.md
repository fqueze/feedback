## Question: which IPC messages did this thread send and receive, in order?

- Command: `profiler-cli thread markers --session bpec-1 --category IPC --list --limit 0`
- Expected: each IPCIn/IPCOut row to name its message type (e.g. `PWindowGlobal::Msg_Destroy`), as the profiler's marker table does.
- Got: rows show only `IPCIn` / `IPCOut` and a duration, no message type, so the list could not tell a `PWindowGlobal::Msg_Destroy` from a `PHttpBackgroundChannel::Msg_OnTransportAndData`. `--search PWindowGlobal::Msg_Destroy` finds them but loses the ordering against the other messages.
- Workaround: `--json` and a script printing `data.messageType`, `data.direction` and `data.recvEndTime` per marker. The default output could show `messageType` (and the direction) as the row's description.

## Question: at what millisecond did each marker in this 80 ms window happen? (review)

- Command: `profiler-cli zoom push 82.170,82.250 --session <s>` then `profiler-cli thread markers --list --limit 0 --session <s>`
- Expected: a time column precise enough to tell apart the rows of an 80 ms window (e.g. `82246.13ms`, or relative to the zoom start).
- Got: every row shows `t=1m22s`, so a Destroy, a load event and a layout flush 5 ms apart look alike. The rows are in the right order, but you cannot see the gaps between them.
- Workaround: `--json` and a script that prints each marker's `start` in ms. The time column could scale its precision to the zoomed range.
