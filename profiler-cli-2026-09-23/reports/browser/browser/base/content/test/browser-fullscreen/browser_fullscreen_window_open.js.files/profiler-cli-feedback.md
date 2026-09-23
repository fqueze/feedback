## Question: which IPC message is this row
- Command: `profiler-cli thread markers --search IPCIn,DOMEvent,Reflow --list --limit 0 --session <s>`
- Expected: the IPC message type (e.g. `PBrowser::Msg_UpdateDimensions`) in the label column, as the Runnable rows have.
- Got: IPCIn/IPCOut rows with an empty label and only a (sometimes negative) duration. It took `marker info m-a..m-b --json` and a script to see the message names.

## Question: when did the receiving thread dispatch this IPC message
- Command: `profiler-cli marker info m-2060 --session <s>` (an IPCIn, `PBrowser::Msg_Show`)
- Expected: the recipient-side dispatch time, stated as such. It is what orders the message against the DOM events on the same thread.
- Got: `Time: 1m42s - 1m42s (-1,102,655ns)`. The start is the sender's send time, the end is the recipient's time, and cross-process clock skew makes the duration negative. The raw payload fields (`startTime`, `recvEndTime`…) are in yet another timebase than the marker's start. I had to infer that the end (start + duration) is the dispatch time, and check it against the preceding `message` DOMEvent.

## Question: in what order, to the millisecond, did these markers happen (review, browser-review-browser_fullscreen_window_open.js)
- Command: `profiler-cli thread markers --session <s> --list --limit 0` inside a 40 ms zoom (`zoom push 101.89,101.93`).
- Expected: start times precise enough to order rows within the zoom, e.g. `101.9232s`.
- Got: `t=1m42s` on every row, so mousedown, IPC dispatch and the refresh tick could not be ordered. Needed `--json` and jq over `.flatMarkers[].start` for every thread checked.
- Suggestion: scale the time column's precision to the view range.

## Question: which thread handle is this link's `thread=yh` (review, browser-review-browser_fullscreen_window_open.js)
- Command: `profiler-cli load '<profiler link>&thread=yf'` opens that one thread. A report's other links on the same profile use `thread=yh`, `yb`, `y7`, `0`.
- Expected: a way to map a link's `thread=` value to a `t-N` handle without reloading the profile once per link, e.g. `profiler-cli thread list` showing each thread's URL value, or `thread select yh`.
- Got: nothing. Worked it out by hand from the URL encoding (`yh` = 2*32+17 = t-81).
