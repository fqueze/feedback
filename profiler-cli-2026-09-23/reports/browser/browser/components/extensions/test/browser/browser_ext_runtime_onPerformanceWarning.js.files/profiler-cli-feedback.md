## `zoom push m-A,m-B` is rejected
- Command: `profiler-cli zoom push m-17918,m-17926 --session browser-onPerfWarn-1`
- Expected: zoom from the start of one instant marker to the other (here, "Leaving test" to the vsync TEST-UNEXPECTED-FAIL).
- Got: `Error: Invalid time value: "m-17918". Expected timestamp name (ts-X), seconds ...`
- Workaround: `marker info --json` to read both starts in ms, then `zoom push 218.21,226.73`.

## `thread markers --search <innerWindowID>` finds nothing
- Command: `profiler-cli thread markers --search "4294967331" --list` (and `innerWindowID:4294967331`)
- Expected: the markers of that window, e.g. the DocumentLoad that names its URL.
- Got: 0 matches, although `--group-by field:innerWindowID` shows 110 markers with that value in the same range.
- Workaround: `--group-by field:innerWindowID,name` in a narrow zoom, then `marker info` on the grouped examples.
- Question it would have answered: "which document is this `RefreshDriverTick waiting for paint` window?". An innerWindowID -> URL mapping (profile.pages) in `marker info` would answer it directly.

## Where is a marker's longest gap? (review)
- Question: "did the leaked window's `RefreshDriverTick waiting for paint` markers ever pause during a vsync wait?"
- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --session browser-review-browser_ext_runtime_onPerformanceWarning.js-1`
- Expected: the frequency analysis's `max=3.682s` interval to say where it is (start time, or the two handles).
- Got: only the duration. Workaround: `--list --limit 0 --json` (12,583 markers) and a script to sort the gaps; the 3.68 s one was before the leak, between two unrelated windows.
