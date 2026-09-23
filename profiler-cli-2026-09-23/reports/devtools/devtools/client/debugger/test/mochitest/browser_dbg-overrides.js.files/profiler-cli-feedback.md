## Which document a Reflow marker reflowed (browser_dbg-overrides.js)

- Question: which `Reflow (sync)` markers on the parent main thread were reflows of browser.xhtml (innerWindowID 2), as opposed to the toolbox or debugger documents.
- Command: `profiler-cli thread markers --session <s> --thread t-0 --search "name:Reflow" --list --limit 0`, then `--search "innerWindowID:2"`.
- Expected: the inner window id (or document URL) of each marker in the list, or `innerWindowID:2` working as a field filter.
- Got: the list shows no window id; `--search innerWindowID:2` matches 0 markers although `marker info` prints `innerWindowID: 2` under "Other payload fields".
- Workaround: `--json` and a Python filter on `flatMarkers[].data.innerWindowID`.

## When a Reflow marker's cause stack was captured

- Question: when was the layout that this reflow flushed dirtied (the cause stack's time), to the millisecond.
- Command: `profiler-cli marker stack m-17530 --session <s>`.
- Expected: the capture time at the precision of the marker times, and a note that for Reflow markers the stack is the cause (when layout was dirtied), not the marker's start.
- Got: `Captured at: 5m4s` (1 s resolution) for a marker that starts at 304.956 s, with nothing saying it is the cause time; for another marker, `Captured at: 4m22s` for a reflow at 5m4s, which looks like a bug until you realise it is the cause.
- Workaround: `marker info --json` and read `stack.capturedAt` (303660.04 ms).
