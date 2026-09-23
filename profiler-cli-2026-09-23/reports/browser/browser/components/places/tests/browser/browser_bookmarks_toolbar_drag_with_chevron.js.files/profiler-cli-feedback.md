## Question: "how big was popup window N at each moment?"

- Command: `profiler-cli thread select t-17; profiler-cli thread markers --search CompositorScreenshot --list --limit 0 --json | python3 ...` to print windowID/windowWidth/windowHeight per screenshot marker.
- Why: the chevron popup (windowID 4) was 191x698 on the passing iteration and 191x892 on the failing one; that size change was the key evidence, and only the JSON payload had it.
- What would have answered it: `profiler-cli screenshots --range a,b` (or `--list`) printing one line per frame with time, window id and size, without writing images. The plain `thread markers --list` row for CompositorScreenshot shows no size.
- Minor: `marker info m-1 m-2 --json` records have no `handle` field, so a multi-handle JSON result cannot be mapped back to the handles asked for.

## Review (browser-review-browser_bookmarks_toolbar_drag_with_chevron.js)

### Question: "in which order did these events happen, 3 ms apart, 72 s into the profile?"

- Command: `profiler-cli thread markers --search "toolbarvisibilitychange,PlacesChevron,popupshow" --list --limit 0` on a profile over 60 s long (task aFfiSkiRTEev9FAjnmKcog).
- Expected: start times precise enough to order the rows, as for the first minute (`t=2.642s`).
- Got: every row reads `t=1m13s`, so underflow / mousedown / popupshowing 3 ms apart cannot be told apart.
- Workaround: `marker info <handles> --json` and printing `start`. Showing `t=72.9070s` (or `1m12.907s`) would have answered it.

### Question: "where is the overflow event on #PlacesToolbarItems?"

- Command: `thread markers --search "RefreshDriverTick,overflow - scrollbox" --list`.
- Expected: the `overflow - scrollbox@… id="PlacesToolbarItems"` row, as printed.
- Got: only the RefreshDriverTick rows; the printed `<type> - <target>` label is not searchable as one string.
- Workaround: search `PlacesToolbarItems`. Matching the printed row text, or saying in `--help` that it is not matched, would have saved a query.
