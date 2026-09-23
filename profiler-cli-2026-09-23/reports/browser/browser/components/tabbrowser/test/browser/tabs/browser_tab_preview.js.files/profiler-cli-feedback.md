## `thread markers --list` timestamps too coarse late in a profile

- Command: `profiler-cli thread markers --category Test --search browser_tab_preview.js --list --limit 0 --session ...`
- Expected: timestamps precise enough to zoom on (ms), e.g. `t=609.095s`.
- Got: `t=10m9s` for every marker in the same second; `TEST-PASS` at 609.09s and the `Entering test` at 608.70s both read `10m9s`, so the text output cannot bound a zoom.
- Workaround: re-ran with `--json` and read `start` (ms).
- Question the default output did not answer: "when exactly, to the ms, did this assertion run?"

## `screenshots --at` returns windows that are long gone, without saying so

- Command: `profiler-cli screenshots --at 609.070 -o shots1/a --session browser-browser_tab_preview.js-1`
- Expected: the windows on screen at 609.07, or each image labelled with its own capture time / window id.
- Got: 4 images, two of them a tab-note panel ("Telemetry test note") from an earlier test, whose last screenshot is 13.5-13.8 s long (m-4367, m-4383). Read at face value it looks like a panel left open during the failure.
- Workaround: `marker info` on each image's marker to see its time range and windowID.
- Question the default output did not answer: "which windows were actually on screen at t?"

## Field search does not match CompositorScreenshot payload fields

- Command: `profiler-cli thread markers --search "windowID:2" --list --session browser-browser_tab_preview.js-1` (GPU GeckoMain thread)
- Expected: the main window's screenshots.
- Got: `0 markers`, while `--group-by field:windowID` on the same thread lists `2: 2068 markers`, and `--search windowWidth:1280` also gives 0. `marker info` says "Fields (raw payload, no schema)", and `--json` flatMarkers carry no `fields` for them.
- Workaround: `--group-by field:windowWidth` with `--search "CompositorScreenshot,windowID:2"` (which, being OR, does not filter by window either), then a python pass over `--json` `data`.
- Question the default output did not answer: "when did window 2 change size?" — a per-window list of size changes over time.


## `marker info --json` changes shape with the number of handles

- Command: `profiler-cli marker info m-599 --json --session browser-review-browser_tab_preview.js-3` after `profiler-cli marker info m-540 m-25 m-44 --json ...`
- Expected: the same shape either way, e.g. always `{"markers": [...]}`.
- Got: several handles give `{"type", "requested", "markers": [...], ...}`; one handle gives the bare marker object (`markerIndex` at top level), so a script written for one breaks on the other (`KeyError: 'markers'`).
- Workaround: re-ran without the script and read the object.
- Question the default output did not answer: "what is the markerIndex of each of these markers?" — the index a review has to match against a link's `marker=N`; the text output of `marker info` does not print it.
