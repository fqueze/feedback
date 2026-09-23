## Marker times in `--list` are rounded to the second past one minute

- Question: at which millisecond did each DOMEvent of the test happen, to order them against each other and against IPC/NotifyObservers markers on other threads.
- Command: `profiler-cli thread markers --session <s> --thread t-197 --search DOMEvent --list --limit 0` (also after `zoom push 93.4,94.2`).
- Expected: a start time with ms resolution (e.g. `t=93.449s` or `t=1m33.449s`), as the view is 800 ms wide.
- Got: every row reads `t=1m33s` or `t=1m34s`; 220 events in the test's 700 ms are indistinguishable in time.
- Workaround: `--json` and jq over `.flatMarkers[].start`.

## `marker info` payload times use another time base than the marker's own time

- Question: when was this IPC message sent, and how long did it wait before the main thread ran it.
- Command: `profiler-cli marker info m-10270 --session <s>` (an `IPCIn` of `PContent::Msg_ClearFocus`).
- Expected: `sendStartTime`/`endTime` comparable with the `start` that `thread markers --list --json` gives for the same marker (93499.166).
- Got: `startTime: 93507.144`, `sendStartTime: 93507.147`, `endTime: 93568.976` under "Other payload fields": raw, not shifted by the ~7.98 ms the list applies, and the human "Time:" line only says `1m33s - 1m34s (61.832ms)`.
- Workaround: use the duration (61.8 ms) and the list's `start`; subtract the offset by hand for the payload's end time.
