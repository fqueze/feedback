## Question: when exactly did each marker happen, within a sub-second zoom

- Command: `profiler-cli zoom push 326.77,327.6 --session …; profiler-cli thread markers --search "DOMEvent,NotifyObservers" --list --limit 0 --session …`
- Expected: start times with ms precision (e.g. `t=326.853s`), at least once zoomed to under a second.
- Got: every row prints `t=5m27s`; 400 rows in an 830 ms zoom are indistinguishable in time, so ordering against another thread or a gap between two markers cannot be read.
- Workaround: `--json` and a script printing `flatMarkers[].start / 1000` with 3 decimals.
- Could have shown: the `t=` column with ms precision (or scale precision to the zoom's duration).


## review-browser_tab_preview.js.bug1980036.tabnotes-timeout: same rounding in `marker info`

- Command: `profiler-cli marker info m-110 --session …` (text output), on a 6-minute profile.
- Expected: the marker's start with ms precision.
- Got: `Time: 6m4s (instant)`. Checking that a quoted `t=326.853` is the right marker needs `--json` for every marker. The multi-handle `--json` also wraps its records in `{"markers": [...]}` while a single handle returns a bare record, so one script has to handle both shapes.
- Workaround: `marker info m-a m-b … --json`, and a script printing `markerIndex` and `start` for each marker.
