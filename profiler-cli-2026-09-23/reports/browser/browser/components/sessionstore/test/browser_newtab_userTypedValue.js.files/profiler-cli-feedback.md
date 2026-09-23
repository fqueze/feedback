## Question: in which order did markers a few milliseconds apart happen, past the first minute of the profile?

- Command: `profiler-cli thread markers --session <s> --search "SSTabRestor,BrowserTestUtils:ContentEvent,sessionstore-single-window-restored" --list --limit 0`
- Expected: a start time precise enough to order markers (e.g. `t=95.7117s` or `1m35.712s`), since the question was whether a load event arrived before or after an observer notification 3 ms later.
- Got: every row printed `t=1m35s` / `t=1m36s`; past 60 s the list rounds to whole seconds, so the order of the race was unreadable. `marker info` also prints `Time: 1m36s - 1m36s (12.500μs)`.
- Workaround: `--list --json` piped to a Python one-liner printing `start` and `label` for each marker. Needed on every list in this investigation (three profiles).
- What the default output could have shown: millisecond start times in `--list` (the `t=2.155s` format used below one minute already has them).

## Question (review): which browser did each `focus` DOMEvent target, in a range?

- Command: `profiler-cli thread markers --session <s> --search "focus - browser" --list --limit 0` (also `name:focus`)
- Expected: the `focus - browser@…` rows the list itself prints.
- Got: nothing. `--search` matches the name and payload fields, not the label the list prints, and a DOMEvent's label is made from several fields.
- Workaround: `--search name:DOMEvent --list --json`, filtering `label` in Python.
- What the default output could have shown: let `--search` match the printed label too, or document which payload key holds the event type.

## Question (review): when did the parent main thread handle an IPC message?

- Command: `profiler-cli thread markers --session <s> --search RestoreDocShellState --list --limit 0 --json`
- Expected: `data.endTime` / `data.recvEndTime` on the same time base as `start`.
- Got: `data.*` times are about 7.8 ms ahead of `start` (for example `start` 94027.91 and `data.startTime` 94035.7). Only `marker info` gives `end` on the profile's timeline (94086.11).
- Workaround: `marker info <m> --json` for each IPC marker.
- What the default output could have shown: the receive-side handling time in the list, on the timeline's base.
