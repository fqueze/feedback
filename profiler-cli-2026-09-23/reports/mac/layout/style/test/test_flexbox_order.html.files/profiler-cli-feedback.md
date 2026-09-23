# profiler-cli feedback (test_flexbox_order.html)

## Question: which IPC messages, in order, did the parent exchange with process X?

- Command: `profiler-cli thread markers --search "otherPid:3553" --list --limit 0 --session test_flexbox_order.html-1` (t-0, parent main thread, zoomed to 90 ms)
- Expected: one row per IPC marker with its message type (`PWindowGlobal::Msg_MakeFrameRemote`, `PBrowserBridge::Msg_Show`, ...), like the Runnable rows show `PBrowser::Msg_Show - priority: ...`.
- Got: rows reading only `IPCIn` / `IPCOut` with a duration, no message type and no direction/peer, so the chronological list says nothing.
- Workaround: `--json` and a python script printing `data.messageType`. The `--group-by field:messageType` view has the types but loses the order, which was the question.

## Preference Read markers swamp every free-text search

- Commands: `profiler-cli profile markers --search TEST-`, `--search xorigin`, `thread markers --search "PBrowser,Load,..." --list` on a CI mochitest profile.
- Expected: the markers I was after.
- Got: pages of `Preference Read` markers whose pref names contain the term (`dom.origin-trials.test-key.enabled`, `network.http.referer.XOriginPolicy`, `browser.cache.max_shutdown_io_lag` for "load"). The parent main thread holds 2.5M markers, most of them Preference Read.
- Workaround: append `-name:Preference Read` to every search. A default exclusion, or a flag to drop a marker name globally for the session, would have saved a round trip on almost every query.

## Field search over a 2.5M-marker thread with --json is very slow

- Command: `profiler-cli thread markers --search "otherPid:3553" --list --limit 0 --json --session test_flexbox_order.html-1` on t-0, no zoom.
- Expected: seconds (the same search zoomed to 90 ms answered at once; a search on three messageType values over the whole thread took 4 s).
- Got: still running after several minutes, blocking the session's other queries.
- Workaround: zoom first, or search on a narrow `messageType:` value.
