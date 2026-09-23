## Question: when did the receiving thread actually handle this IPC message?

- Command: `profiler-cli thread markers --search RequestContentRepaint --list --limit 0 --session <s>` on the content main thread, then `marker info m-N`.
- Expected: for an `IPCIn` marker, the time the recipient thread dispatched the message, next to the time it was sent. Here it decided whether a repaint request reached content before a timer callback 0.2 ms later.
- Got: `--list` prints one start time (the send time, coarse `t=1m42s`) and a duration; that the end of the interval is the recipient's dispatch time is only discoverable by noticing that a message sent during a 100 ms busy loop "lasts" until the loop ends. `marker info` prints `Time: 1m42s` with no end.
- Workaround: `marker info --json`, read `start`/`end`.
- What would have answered it: in `--list` and `marker info` for IPC markers, "sent 102207.47 ms, handled on <thread> 102207.72 ms (+0.24 ms)".

## Question: which marker does a link's `marker=N` point to? (review-test_group_keyboard.html.backward-jump)

- Command: `profiler-cli load '<profiler.firefox.com link with marker=318893&thread=xi>' --session <s>`, then `thread markers --list --json` in a zoom around the quoted time, then `marker info m-a m-b ... --json` on every marker there.
- Expected: `load` to report the handle of the marker the link selects, or `--list --json` rows to carry `markerIndex`.
- Got: `load` prints the selected thread only; flat-list rows have no `markerIndex`. Checking 38 links needed a script calling `marker info` on every marker in a ±1 ms window. Also `marker info` returns an object for one handle but `{"markers": [...]}` for several, which broke the first version of the script.
- Workaround: the script (`findidx.py` in `review/`).
- What would have answered it: `load` printing "link selects marker m-N (<name>, <start>)", or a `marker find --index N`.
