## Question: which IPC messages did this thread send/receive around t, and when?

- Command: `profiler-cli thread markers --session dsface-1 --thread t-130 --category IPC --list --limit 0` (and the same with `--search PBrowser`)
- Expected: each IPCIn/IPCOut row to name its `messageType` (e.g. `PBrowser::Msg_UpdateDimensions`) and the other pid, as the profiler UI does.
- Got: rows read only `IPCIn` / `IPCOut` with an empty description, so the list cannot answer the question; I had to pipe `--json` through a Python script to print `data.messageType`, `data.otherPid`, `data.direction`. Done four times in this investigation (test process, harness, parent, per profile).
- Workaround: `--json` + script, or searching the `Runnable` markers, which do carry the message name.

## Question: when did this DOM event (as displayed) fire?

- Command: `profiler-cli thread markers --session dsface-1 --thread t-129 --search "PBrowser::Msg_UpdateDimensions,PBrowser::Msg_Show,load - document,mousedown,resize - window" --list --limit 0`
- Expected: the `load - document` and `resize - window` DOMEvent rows, since that is the text the list prints for them.
- Got: no DOMEvent rows at all; the displayed label is composed from payload fields (eventType + target), so a term containing " - " matches nothing, silently.
- Workaround: `--search DOMEvent` and `grep` the printed lines.
