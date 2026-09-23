# profiler-cli feedback (browser_fullscreen_window_open.js)

## Question: which IPC messages did this process receive, and in what order relative to its DOM events?

- Command: `profiler-cli thread markers --session <s> --search IPCIn --list --limit 0`
- Expected: each row naming the message type (e.g. `PBrowser::Msg_UpdateDimensions`), with a
  time comparable to the DOMEvent rows of the same thread.
- Got: every row labelled just `IPCIn`, no message type. Needed `--json | jq
  '.data.messageType'` to see them.
- Worse: the row time (`start`) of an `IPCIn` marker is the *sender's* send time, shifted
  (here by ~6.76 ms, it seems the receiving process' offset), so `PContent::Msg_WindowPostMessage`
  listed at 101923.31 appeared *after* the `message` DOMEvent it causes (101922.23). The receiver's
  own clock is only in `data.endTime`/`data.recvEndTime`, and those are not in the displayed base
  either. Ordering an IPC receipt against a DOM event on the same thread took several rounds of
  guessing at the time bases.
- Workaround: compare `data.endTime` values of IPCIn markers with each other only, within one
  receiving thread.
- Could have shown: the message type in the label (`IPCIn PBrowser::Msg_Show`), and the receive
  time on the receiving thread in the same base as that thread's other markers.

## Question (review): in what order, to the millisecond, did these markers happen on one thread?

- Command: `profiler-cli zoom push 101.89,101.97 --session <s>` then
  `profiler-cli thread markers --session <s> --list --limit 0`
- Expected: row times at a precision that matches the 80 ms view, e.g. `t=101.9232s`.
- Got: every row printed as `t=1m42s`. Inside the zoom, the rows cannot be told apart in time.
  Ordering the iframe's `message`, `pointerdown` and `PBrowser::Msg_Show` needed `--json` and a
  script that prints `start`.
- Could have shown: times with as many decimals as the current view range needs, such as ms
  inside a sub-second zoom.
