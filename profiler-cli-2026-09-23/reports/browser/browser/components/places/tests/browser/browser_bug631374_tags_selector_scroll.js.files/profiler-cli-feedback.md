## `thread markers --list` times are rounded to the second (browser_bug631374_tags_selector_scroll.js)

- Question: in what order did the markers of a 160 ms window come (a Reflow, a SetURI event, a click, an unhandledrejection)?
- Command: `profiler-cli zoom push 80.100,80.260 --session S` then `profiler-cli thread markers --category Layout --list --limit 0 --session S`
- Expected: start times precise enough to order markers inside the zoom (ms, e.g. `t=80.1667s`).
- Got: every row shows `t=1m20s`, so the list cannot order anything shorter than a second, and rows are not sorted by start either.
- Workaround: `--json` and a script that prints `start` in ms and sorts by it.
- Same thing in `profiler-cli screenshots --range 80.100,80.400 -o dir`: 20 screenshots all listed as `t=1m20s`; I had to run `marker info m-N --json` on each to find out which screenshot came before the click.

## Markers cannot be told apart, or filtered, by window (review of browser_bug631374_tags_selector_scroll.js)

- Question: which window's refresh driver ticked or reflowed between two events (three pres shells, windows 2, 470 and 479, tick on the same main thread)?
- Command: `profiler-cli thread markers --search RefreshDriverTick,Reflow --list --session S`; then `profiler-cli thread markers --search 'innerWindowID:470' --list --session S`
- Expected: an innerWindowID (or the window's URL) on each row; the second command to keep only window 470's markers.
- Got: no window on any row; the `innerWindowID:470` search matches 0 markers, although `--json` shows `data.innerWindowID: 470` on them.
- Workaround: `--json` and a script printing `data.innerWindowID` next to each marker.
