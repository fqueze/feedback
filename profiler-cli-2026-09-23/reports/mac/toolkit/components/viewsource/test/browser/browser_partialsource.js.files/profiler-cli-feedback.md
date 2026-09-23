# profiler-cli feedback (browser_partialsource.js)

## IPC markers in `thread markers --list` carry no message type or peer

- Command: `profiler-cli thread markers --search SetFocusedBrowsingContext --list --session browser_partialsource.js-1`
- Question: "which IPC messages did the parent get from, and send to, which process, in what order?"
- Expected: IPCIn / IPCOut rows showing the message type and the other side (for example `PContent::Msg_SetFocusedBrowsingContext <- Web Content (4/6)`).
- Got: rows reading only `IPCIn` / `IPCOut` with a duration; the type and peer are visible only through `marker info` on each handle.
- Workaround: `marker info m-445 m-475 m-478 m-483`, one record per handle.

## IPC payload times use another time base than the displayed `t=`

- Command: `profiler-cli marker info m-445 --session browser_partialsource.js-1`
- Expected: `startTime` / `sendStartTime` / `recvEndTime` in the same base as `Time: 1.688s - 1.694s`.
- Got: `startTime: 1695.73225`, `endTime: 1702.208083`, about 7.9 ms off the displayed range, with no hint of the offset. Comparing the send time of an IPC message with a marker on another thread meant working the offset out by hand.
- Workaround: subtracted (payload endTime - displayed end) from every payload time.

## `profile markers --search` does not match a bracketed JSActor label

- Command: `profiler-cli profile markers --search 'ViewSource:GetSelection,[SpecialPowers] Spawn,[BrowserTestUtils] load,UnsuppressAndInvalidate' --session browser_partialsource.js-2`
- Expected: the `ReceiveQuery [SpecialPowers] Spawn` and `SendAsyncMessage [BrowserTestUtils] load` markers too.
- Got: only the `ViewSource:GetSelection` and `UnsuppressAndInvalidate` ones.
- Workaround: per-thread `thread markers --search Spawn,SendAsyncMessage` and filtering the text.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session browser_partialsource.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message did say what to do.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.

## `marker info` rounds times to the millisecond (review-browser_partialsource.js)

- Question: "of two markers 35 µs apart on different threads, which came first?" (the parent's `SendQuery [SpecialPowers] Spawn` vs the `SetFocusedBrowsingContext` sent by a content process)
- Command: `profiler-cli marker info m-657 m-743 --session review-browser_partialsource.js-1`
- Expected: start and end at µs precision, in the displayed time base.
- Got: `Time: 1.688s (instant)` and `Time: 1.688s - 1.694s (6.476ms)`: both round to the same millisecond, so the order is not readable. The only sub-ms times shown are the IPC payload ones, in another time base (see the entry above).
- Workaround: `marker info ... --json` and a script printing each record's `start` / `end`.
