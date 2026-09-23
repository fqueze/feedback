## review-browser_ext_management.js: which window's refresh driver produced these markers?

- Question: 7,628 "RefreshDriverTick waiting for paint" markers on the WebExtensions main thread; which document's refresh driver is each from?
- Command: `profiler-cli thread markers --session S --search "innerWindowID:4294967331" --json` (also with `--list`)
- Expected: the markers whose payload has that innerWindowID; `innerWindowID` is shown under `rawFields` by `marker info --json`.
- Got: 0 matches. `--list` output does not show innerWindowID either, so a marker's document cannot be seen without `marker info` one at a time.
- Workaround: `thread markers --search "RefreshDriverTick waiting for paint" --list --limit 0 --json` and a Python count over `flatMarkers[].data.innerWindowID`. What would have answered it: `--search innerWindowID:N` matching raw fields, ideally with the window's URL resolved from the page table. Not tried: `--group-by field:innerWindowID`, which may already work.
