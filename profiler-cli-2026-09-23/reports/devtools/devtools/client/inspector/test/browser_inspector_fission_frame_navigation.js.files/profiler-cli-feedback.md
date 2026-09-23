## Test log timestamps too coarse past one minute

Question: at which millisecond did each line of the test's log happen, to line it up with other threads' markers?

Command: `profiler-cli thread markers --category Test --search browser_inspector_fission_frame_navigation --list --limit 0 --session <s>`

Expected: a start time precise to the millisecond (e.g. `t=62.048s`), as is needed to zoom between two log lines.
Got: `t=1m2s` for every marker between 62.0 s and 62.9 s, so 50 consecutive log lines read as simultaneous.
Workaround: `--json` and reading `flatMarkers[].start`.

## Flat marker list: no way to see millisecond times and payload together

Question: in what order, to the millisecond, did these document and IPC events happen in the content process (t-48, 62.04-62.40 s)?

Command: `profiler-cli thread markers --search "...,Msg_SubFrameCrashed,..." --list --limit 0 --session <s>` (thread t-48, zoomed)

Expected: each row with a ms-precision time and its payload summary (the text output has the summary).
Got: text output has the summary but `t=1m2s` for every row; `--json` has precise `start` but `description` is empty, the payload only in `fields[]`.
Workaround: a script over `--json` joining `fields[].formattedValue` (`flat.py` in this directory).

