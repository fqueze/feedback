## Question: "at what time was the sample that contains function X?"

- Command: `profiler-cli thread samples-top-down --search onDocEvent` after `zoom push 8.745,8.790`
- The call tree showed one sample in `connector/index.js!onDocEvent`, but not when it was taken. I needed to know whether it came before or after an RDP packet 7 ms later.
- Workaround: looped `zoom push` over 10 ms windows and grepped each call tree. A `--list`/timestamps option on `thread samples --search` (the times of the matching samples) would have answered it in one call.

## Default thread after `load` is not the parent main thread

- Command: `profiler-cli load <per-test mochitest profile URL> --session <id>`
- Expected: the parent process GeckoMain (t-0) selected, or no selection at all.
- Got: `Selected thread: t-21 (GeckoMain, WebExtensions)`. A `thread markers --category Test` right after `load` would have searched the wrong thread without any warning.
- Workaround: ran `thread select t-0` explicitly.

## (review) Raw payload times in `marker info` are in another time base than `Time:`

- Command: `profiler-cli marker info m-45 --session review-har_copy_all-1` (a Network marker), and `marker info m-11` (an IPCIn marker) in another profile.
- Expected: the payload's `startTime`/`endTime`/`sendStartTime`/`recvEndTime` comparable with the `Time:` line and every other marker's time.
- Got: `Time: 8.739s - 8.739s` but `startTime: 8749.088373` (10.2 ms off); on the IPC marker, `Time: 5.889s - 5.896s` but `startTime: 5895.887`, `sendStartTime: 5895.888` (7.0 ms off). The offset differs per profile, so the raw values cannot be compared with anything.
- Also: the IPCIn marker's displayed start is the sender's send time, and its end the receiver's dispatch. That is what answered "when did the content process send the second navigation's messages", but the output does not say so.
- Workaround: used only the displayed start/end, after checking they matched other markers. Printing the payload times in the display base, labelled (sent, received on IO thread, dispatched), would have answered it directly.

## (review) `profiler-cli session stop <id>` fails with an unrelated message

- Command: `profiler-cli session stop review-har_copy_all-2`
- Expected: the session stopped, or "use `profiler-cli stop <id>`".
- Got: `error: too many arguments for 'list'. Expected 0 arguments but got 2: stop, review-har_copy_all-2.`
- Workaround: `profiler-cli stop <id>`.
