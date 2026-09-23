## Marker times lose millisecond resolution past one minute

- Question: at what millisecond did each step of one subtest happen (keydown, rAF, setTimeout, `shown`), to order a race?
- Command: `profiler-cli thread markers --session browser_topsites_section.js-2 --search "DOMEvent,..." --list --limit 0` (and `marker info m-28 m-16 m-32`) on a 1m29s profile.
- Expected: `t=86.202s`-style times, as the same command prints on a 22 s profile (`t=12.178s`).
- Got: every row `t=1m26s`, and `marker info` gives `Time: 1m26s (instant)`, so a 5 ms race is unreadable.
- Workaround: `--json` and a Python one-liner printing `start/1000` with three decimals.

## Which document a burst of markers belongs to

- Question: was the 190 ms `PWindowGlobal::Msg_RawMessage` jank in the test's page or in another about:newtab in the same process (the preloaded one)?
- Command: `profiler-cli thread markers --session browser_topsites_section.js-1 --list` then `marker info <every handle> --json`, grouping `rawFields.innerWindowID` in Python.
- Expected: a way to show or group by inner window (or its URL) in `thread markers`, e.g. `--group-by field:innerWindowID`, or the window ID printed in `--list` rows.
- Got: innerWindowID only in `marker info --json` `rawFields`.
- Workaround: script over `marker info --json` for ~150 handles.

## Does this handle match the link's `marker=N`? (review-browser_topsites_section.js)

- Question: which `m-…` handle is the marker a report link points at (`marker=41988`)?
- Command: `profiler-cli marker info m-2 m-16 --json --session review-topsites-1`, then Python to print `markerIndex`.
- Expected: the marker index printed in the default `marker info` output, or a `marker info --index 41988`.
- Got: the default output has no index. Also, `--json` returns an object for one handle and a list for several, so the same one-liner breaks (`TypeError: string indices must be integers`) when you switch between them.
- Workaround: `--json` plus a script that handles both shapes.
