## Marker times rounded to whole seconds in long profiles
- Command: `profiler-cli thread markers --category Test --search browser_tab_preview.js --list --limit 0` and `marker info m-13 m-16` on a 10m28s profile.
- Expected: times with millisecond precision (e.g. t=594.762s), needed to order test events that are 50 ms apart.
- Got: `t=9m55s`, `t=10m` - rounded to the second, both in the list and in `marker info`.
- Workaround: `--json` and read `start`.

## Question: "when did window N change size, and what size is it at time t"
- Command: `profiler-cli thread markers --search CompositorScreenshot --list --limit 0 --json` on the GPU process thread, scripted to print windowID/windowWidth/windowHeight whenever they change.
- What the default output could have shown: `profiler-cli screenshots` (or a `screenshots --list`) listing each window id with its size and the times its size changed. The window being un-maximized by an earlier test (1280x976 -> 1152x878) was the key fact, and it is only visible in raw payloads.
- Workaround: the script above over `--json`.

## Question: "DOM events in order, with target and ms timestamps"
- Command: `thread markers --search DOMEvent --list --limit 0 --json`, scripted to print `start` in ms, `eventType` and `target`.
- What the default output could have shown: in `--list`, ms-precision times (see the rounding item above) plus the event type and target in the label, so that a mouseover on `hbox.tab-content` of a given tab reads without JSON.

## Question: "is the marker this link selects (marker=N) the one the report quotes" (review-browser_tab_preview.js.bug1980036)
- Command: `thread markers --list --limit 0 --json` in a zoom, then a batched `marker info m-… m-… --json`, scripted to print `markerIndex`, `start` and name.
- What the default output could have shown: `markerIndex` in `thread markers --list --json` rows, or a lookup by index (e.g. `marker info idx:1339383`). Checking a report's 20 links took a script.
