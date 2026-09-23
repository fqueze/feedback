## Question: "which document (innerWindowID) is each Reflow / DoFlushPendingNotifications / RefreshObserver marker for"

- Command: `profiler-cli thread markers --search "Reflow,DoFlushPendingNotifications,RefreshDriverTick,RefreshObserver" --list --limit 0 --session <s>`
- Expected: a way to tell the new browser window's flushes from the other windows' (with two browser windows and in-process about: pages, they interleave within milliseconds).
- Got: the list has no innerWindowID column; it is only in `marker info` ("Other payload fields" / `rawFields`), one record per handle.
- Workaround: collected the handles with `--list --json`, ran `marker info <all handles> --json`, and tabulated `rawFields.innerWindowID` plus the top frames with a script. Could show: an innerWindowID column (or the document URL) in `--list`, and `--group-by field:innerWindowID` / `--search innerWindowID:150` documented for raw payload fields.

## Review: same question, "which document (innerWindowID) is each flush / RefreshDriverTick marker for"

- Command: `profiler-cli thread markers --search 'DoFlushPendingNotifications,RefreshDriverTick,...' --list --limit 0 --session <s>`
- Got: the text list has no innerWindowID, so the other windows' Layout flushes look like the new window's. The same `--list --json` output does carry it, as `flatMarkers[].data.innerWindowID`, which is a cheaper workaround than one `marker info` per handle. `marker info --json` puts it under `rawFields`, not `fields`.
- Could show: innerWindowID in the text list, at least when the thread holds several documents.
