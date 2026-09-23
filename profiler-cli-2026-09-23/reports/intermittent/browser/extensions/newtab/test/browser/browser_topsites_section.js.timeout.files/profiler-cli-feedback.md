## Which IPC message is this IPCIn/IPCOut marker, and when was it handled?

- Command: `profiler-cli thread markers --session <s> --search "IPCIn" --list --limit 0` (zoomed to 240 ms around a tab switch)
- Expected: each IPC row to carry its message type (e.g. `PBrowser::Msg_OnStateChange`, `PWindowGlobal::Msg_RawMessage`) and the peer pid, the way DOMEvent rows carry the event type and target.
- Got: rows reading only `IPCIn` / `IPCOut` with a duration, so the ordering question ("did the STATE_STOP OnStateChange land before or after the JSActor load message?") could not be answered from the list.
- Workaround: `profiler-cli marker info m-A m-B ... --json` piped through a Python script to print `fields.messageType`, `start`, `end`. Needed on three profiles.

## In what order did this thread handle these IPC messages? (review)

- Command: `profiler-cli thread markers --session <s> --search "messageType:PBrowser::Msg_OnStateChange,messageType:PWindowGlobal::Msg_RawMessage" --list --limit 0` on the parent main thread, zoomed to a tab switch
- Expected: a way to list IPCIn rows ordered by when the receiving thread handled them (marker end), next to the DOMEvent/Runnable markers those handlers produced, with the message type on each row.
- Got: IPCIn rows sorted by send time (start). Messages sent 0.5 ms apart but handled 21 ms later showed up as neighbours, far from the `ContentEvent:load` and `switchTab` markers they have to be ordered against. There is no message type on the row either.
- Workaround: `marker info <handles> --json` piped through a Python script that sorts by `end`. Needed on all three profiles.
