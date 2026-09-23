## The chronological sequence of IPC messages, with their message types

Question: in what order were the PWindowGlobal / PBackgroundIDB* messages sent and received around the failure?

Command: `profiler-cli thread markers --search IDB --list --limit 0 --session S`

Expected: each IPCIn/IPCOut row showing its message type (`PWindowGlobal::Msg_Destroy`, ...), as `--group-by name,field:messageType` does in aggregate.

Got: rows read `m-316  IPCOut  t=10.636s  368.93ms  ✗` with no detail; the type only appears in `marker info`, one marker at a time. `--group-by` gives the types but loses the order.

Workaround: `--list --json` piped through a small script printing `fields[messageType]` per row. The default list could show the IPC marker's messageType (and direction/other thread) as its detail text, as it does for Text markers.
