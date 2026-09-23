## IPC marker payload times are not on the marker timeline

- Command: `profiler-cli thread markers --search 'messageType:UpdateDimensions' --list --limit 0 --session <s> --json`
- Expected: `data.startTime`, `data.sendStartTime`, `data.recvEndTime` on the same zero as the marker's `start` (and as the other threads' markers).
- Got: every IPC marker's payload times are ~7.7 ms later than its own `start` (e.g. `start=311519.825`, `data.sendStartTime=311527.527`); the receiving thread's `Runnable PBrowser::Msg_UpdateDimensions` marker is at 311519.886, i.e. on the `start` timeline, not the payload one. Ordering an IPC against other markers by payload time gives the wrong order.
- Workaround: use the marker `start` only, ignore payload times.

## Question: "at what millisecond did each of these markers happen?"

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0`
- The list prints `t=5m11s` for every marker: all 28 markers of the test's log share two values, so the order of events 10-50 ms apart (the test's check vs. an IPC vs. a reflow) cannot be read from it.
- Could have shown: `t=311.512s` (ms resolution) in the list once the profile is longer than a minute, or when `--list` is used with a zoom.
- Workaround: `--json` and a script printing `start`.

## Default session directory not writable

- Command: `profiler-cli load <url> --session <s>` in a sandbox.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested `PROFILER_CLI_SESSION_DIR`, which worked. One wasted call; fine as is.
