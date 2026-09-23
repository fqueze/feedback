## Question: "in which order did the parent main thread handle these IPC messages, and what were they?"

- Command: `profiler-cli thread markers --search IPCIn --list --limit 0 --session <s>` (zoomed on 50 ms)
- Expected: one row per message with its `messageType` (e.g. `PBrowser::Msg_OnStateChange`), sender, and the send and receive times.
- Got: rows showing only `IPCIn`, the send start time and a duration, no message type and no sender; sorted by send time, not by when the receiving thread handled them.
- Workaround: `--json` and a script reading `fields[messageType]`, `sendThreadName`, and `start + duration` as the receive time, sorted on that.
- What the output could show: the message type in the description column (as `marker info` does), and optionally `--sort end` for the receiving-side order.

## `marker info --json` on IPC markers: no `end` in `thread markers --json` rows

- Command: `profiler-cli thread markers --search "IPCOut,DOMEvent,SendAsyncMessage" --list --json`
- Got: instant markers have no `duration` key at all (KeyError in a script), rather than `0`/`null`.
- Workaround: `m.get('duration') or 0`.
