## `thread markers --list` rounds timestamps to whole seconds in long profiles, even when zoomed

- Question: "at what time (ms precision) did each test-log marker / DOM event fire around the failure?"
- Command: `profiler-cli zoom push 148.5,151 --session S; profiler-cli thread markers --category Test --search <test> --list --limit 0 --session S`
- Expected: `t=` column with ms precision (e.g. `t=148.569s`) once zoomed to a 2.5 s range, so event ordering within one second is readable.
- Got: every row `t=2m29s` (profile is 3m57s long), so 20 markers in the same second are indistinguishable in time; the ordering is only the list order.
- Workaround: `--json` and read `.flatMarkers[].start` with jq.

## (review) index and duration of several markers at once

- Question: "what are the `markerIndex` and duration of these 5 markers?" (to check a report's `marker=N` links and quoted durations).
- Command: `profiler-cli marker info m-8 m-13 m-14 m-18 m-60 --session S` prints a full multi-line record per marker; with `--json`, one handle returns the marker object but several return `{markers:[...]}`, so the first jq filter (`.markerIndex`) returned null.
- Expected: a compact mode (one line per marker: handle, markerIndex, start, duration, name), and the same JSON shape for one or several handles.
- Workaround: `--json | jq '.markers[] | ...'`.
