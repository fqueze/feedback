## Marker search by a raw payload field matches nothing, though --group-by sees the field

- Question: which markers belong to one window (innerWindowID 8589934634) on the WebExtensions main thread?
- Command: `profiler-cli thread markers --search 'innerWindowID:8589934634' --session browser-browser_ext_tab_runtimeConnect.js-2` (thread t-21)
- Expected: the 11,959 `RefreshDriverTick waiting for paint` markers of that window.
- Got: `0 markers (filtered from 240400)`, while `thread markers --search 'RefreshDriverTick waiting for paint' --group-by field:innerWindowID` on the same thread lists `8589934634: 11959 markers`. The markers are `NoPayloadUserData` ("raw payload, no schema").
- Workaround: `--group-by field:innerWindowID` under a zoom, to count per window.

## Times in `thread markers --list` are rounded to the second past one minute

- Question: how long did the test wait between `Leaving test` and its `waiting for vsync to be disabled` failure?
- Command: `profiler-cli thread markers --category Test --search browser_ext_tab_runtimeConnect.js --list --limit 0 --session browser-browser_ext_tab_runtimeConnect.js-1`
- Expected: millisecond times, as for markers under a minute (`t=9.631s`).
- Got: `t=4m32s` for `Entering test`, all its `TEST-PASS`, `Leaving test`, and `t=4m40s` for the failure: neither the order within one second nor the wait length can be read.
- Workaround: `marker info m-6 m-8 m-9 m-16 --json` and read `start` (271575.3 ms, 279880.2 ms).

## Which processes still consume vsync during a wait, per message type (review)

- Question: during the 8.3 s vsync wait, which threads receive `PVsync::Msg_Notify`, which send `Msg_Observe`/`Msg_Unobserve`, and until when does the compositor get `PVsyncBridge::Msg_NotifyVsync`?
- Command: `profiler-cli profile markers --search PVsync --session browser-review-browser_ext_tab_runtimeConnect.js-1` under `zoom push 271.578,279.880`
- Expected: a per-thread breakdown by marker name and `messageType`, with first/last time, as `thread markers --group-by` gives for one thread.
- Got: only a per-thread total (`t-21 996`, `t-0 518`, `t-17 30`, `t-14 7`) mixing IPCIn, IPCOut and Runnable of all message types; `profile markers` has no `--group-by`, and the default list shows `IPCIn`/`IPCOut` rows without their message type.
- Workaround: `--json`, then jq `group_by([.threadHandle, .name, .data.messageType])` with first/last `start`.
