
## `--search innerWindowID:<id>` matches nothing while `--group-by field:innerWindowID` works
- Command: `profiler-cli thread markers --session browser-onCreatedNavTarget-1 --search innerWindowID:4294967338 --list` (thread t-32, WebExtensions)
- Expected: the ~21k "RefreshDriverTick waiting for paint" markers and the DocumentLoad of that window.
- Got: "No markers match the specified filters", although `--group-by field:innerWindowID` groups 21358 markers under 4294967338 and `marker info --json` lists innerWindowID in rawFields. A bare `--search 4294967338` also matched nothing.
- Workaround: list DocumentLoad markers and read `marker info --json` rawFields one by one.

## Question: "which page is innerWindowID N?"
- No command maps an innerWindowID to its URL (the profile's `pages` table). I had to find the DocumentLoad marker with the same innerWindowID by hand to learn that 4294967338 was moz-extension://.../popup.html.
- Could have shown: the page URL next to innerWindowID in `marker info`, and in `--group-by field:innerWindowID` rows.

## Review (2026-09-22): which handle is the marker a link's `marker=N` points at?

- Question: a report link carries `marker=1115807` on the parent main thread; which `m-` handle is it, so I can check it is the quoted `PVsync::Msg_Notify`?
- Command: `profiler-cli thread markers --session <s> --search PVsync --list --limit 0 --json` — `flatMarkers[]` entries have `handle`, `start`, `fields`, but no `markerIndex`.
- Workaround: passed all 450 handles to `profiler-cli marker info <450 handles> --json` and filtered on `markerIndex` with python.
- Expected: `markerIndex` in `thread markers --json` rows, or a `marker info --index N` / `marker find --index N` that returns the handle directly. `load <link>` could also print the handle of the link's selected marker.
