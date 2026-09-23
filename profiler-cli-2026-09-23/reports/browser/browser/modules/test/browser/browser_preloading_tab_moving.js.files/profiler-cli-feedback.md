## Question: which marker belongs to which browser/tab?

- Needed: which `PBrowser::Msg_OnLocationChange` IPC (parent main thread) belongs to which `<browser>`, to tie a location change to the browser the test was listening on.
- Command: `profiler-cli thread markers --session S --search "PBrowser::Msg_OnLocationChange" --list --limit 0 --json`
- Got: IPC and Runnable markers with only otherPid and message type; no browsing context or innerWindowID, while the child's `DocumentLoad` markers carry an innerWindowID. Nothing links the two.
- Workaround: reasoned from timing order and count (two new documents, two IPCs after the wait).
- What could have shown it: IPC markers for PBrowser/PWindowGlobal carrying the browsing context id, or a `marker info` cross-reference to the child-side marker with the same message.
