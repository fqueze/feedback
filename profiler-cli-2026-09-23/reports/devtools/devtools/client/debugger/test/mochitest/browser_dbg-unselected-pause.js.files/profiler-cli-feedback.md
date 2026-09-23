## Marker times in `--list` are rounded to the whole second, even when zoomed in

- Command: `profiler-cli zoom push 353.69,353.95 --session S; profiler-cli thread markers --session S --category Network --list --limit 0`
- Expected: timestamps precise enough to order markers inside a 260 ms zoom (e.g. `t=353.744s` or `5m53.744s`).
- Got: every row reads `t=5m54s`, so the order of 28 markers inside the same second (AsyncOpen, OnStartRequest, Suspend, OnStopRequest, the content-side abort) cannot be read.
- Question I needed answered: "in which order did these network markers happen, and how far apart".
- Workaround: `--list --json` and read `.flatMarkers[].start` with jq.

- Follow-up: `--list --json` does not return a flat list either; it returns the `byType` aggregate. Workaround: `--json --top-n 100000` and flatten `.byType[].topMarkers[]`, sorted by `.start`, with jq.

## Question: "which IPC messages did the parent send to process X, in order"

- Command: `profiler-cli thread markers --session S --search 'otherPid:4865' --list --limit 0`
- Expected: one row per message with its `messageType` (e.g. `PContent::Msg_LoadURI`, `PDocumentChannel::Msg_RedirectToRealChannel`).
- Got: every row is labelled just `IPCOut` / `IPCIn`. The message type only shows in `marker info`.
- Workaround: collect the handles with `--json --top-n 100000`, pass all 150 to `marker info … --json`, and print `.fields[] | select(.key=="messageType")` with jq. That was how I found that `PContent::Msg_AddLoadedOrigin` was never sent to the new process.
- Suggestion: use the IPC message type (and direction/peer) as the list label for IPC markers, the way Runnable markers show their task name.

