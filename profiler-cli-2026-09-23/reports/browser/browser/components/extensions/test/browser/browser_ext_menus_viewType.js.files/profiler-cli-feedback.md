## Which IPC messages arrived in a window (question: "did the new tab's content process get PBrowser::Msg_UpdateDimensions before the synthesized click?")

- Command: `profiler-cli thread markers --session <s> --search IPCIn --list --limit 0` (thread t-147, zoomed to 70 ms)
- Expected: each row to show the IPC message type (e.g. `PBrowser::Msg_UpdateDimensions`), as the profiler UI's marker table does.
- Got: rows show only `IPCIn  t=42.544s  21.832μs` — no message type, so the list could not answer the question at all.
- Workaround: `--list --limit 0 --json` and a Python script printing `data.messageType` per marker. The IPC message type is the one field that matters for IPC markers; it should be in the default one-line label.
## Which handle is the marker a link's `marker=N` points to? (review of this report)

- Command: `profiler-cli load '<profiler URL with marker=134862&thread=Aj>'`, then `thread markers --list --json` (its `flatMarkers` have no `markerIndex`), then `marker info m-1..m-1369 --json`.
- Expected: `load` to print the handle of the marker in the URL, or `marker info --index N`.
- Got: `load` prints only the thread. `marker info` accepts at most 256 handles per range, so checking 11 links meant dumping every marker in a 60 ms window and calling `marker info` six times per thread, in chunks of 256.
- Workaround: the chunked loop plus a Python index→handle map.
