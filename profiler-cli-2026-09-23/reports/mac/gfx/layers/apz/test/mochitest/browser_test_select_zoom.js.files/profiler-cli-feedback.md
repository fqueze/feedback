# profiler-cli feedback (browser_test_select_zoom.js)

## Question: which IPC message is each IPCIn/IPCOut row, in time order, next to the DOM events?

- Command: `profiler-cli thread markers --search messageType:Touch,messageType:APZ,DOMEvent --list --limit 0 --session browser_test_select_zoom.js-1` (parent main thread, 45.30-45.40 s zoom)
- Expected: each IPC row to show its message type (`PBrowser::Msg_SynthesizeNativeTouchPoint`, `PAPZCTreeManager::Msg_SetTargetAPZC`, ...) and its direction, like DOMEvent rows show `pointerover - stack@... class="browserStack"`.
- Got: IPC rows with an empty description (`m-1885  IPCIn  t=45.325s  26.534μs  ✗`), so the list can't tell a touch IPC from a vsync one. `--group-by field:messageType` gives the types, but you lose the order, and the order was the whole question here: which touch hit chrome before the flush.
- Workaround: `--json` piped into a Python script that prints `start`, `name`, `messageType`, `sendThreadName -> recvThreadName`, `eventType`, `target` for each flat marker. Needed six times in this investigation.
- What the default output could show: `IPCIn  PBrowser::Msg_SynthesizeNativeTouchPoint  (http://mochi.test (24/24) -> Parent)` as the description.

## Negative IPC durations

- Command: `profiler-cli thread markers --search IPC --group-by field:messageType --session browser_test_select_zoom.js-1` on t-24 (a content process), 37.45-37.6 s
- Got: `PBrowser::Msg_RealTouchMoveEvent: 9 markers (avg=-55,686ns, max=-44,213ns)`, and IPCIn rows listed with `-31,005ns` durations.
- Expected: non-negative durations, or a note that the clocks of the two processes disagree. Not a blocker, but it makes `--min-duration` filters on IPC unreliable.
