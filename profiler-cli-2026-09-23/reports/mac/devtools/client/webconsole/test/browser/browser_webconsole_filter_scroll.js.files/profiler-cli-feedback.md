## Marker list times are rounded to the second in a long profile, even when zoomed

Question: in which order did the test's steps and the DOM events between them happen?

Command: `profiler-cli thread markers --category Test --search browser_webconsole_filter_scroll --list --limit 0 --session <s>` (also after `zoom push 257.5,259.5`)

Expected: marker times with millisecond precision (e.g. `t=258.412s`), at least once zoomed into a 2 s window.

Got: every row reads `t=4m18s`; the whole test (20+ steps) happens within that second, so the list cannot order anything against other markers.

Workaround: `--json` and print `flatMarkers[].start` myself.


## `thread samples-top-down` prints an empty tree, without saying there are no samples (review)

Question: what was the main thread doing during a 33 ms keypress event (was it idle)?

Command: `profiler-cli thread samples-top-down --include-idle --limit 15 --session review-browser_webconsole_filter_scroll.js-1` after `zoom push 258.1669,258.2002`

Expected: "No samples in the current view", like `thread samples` prints for the same zoom.

Got: the header and `Top-Down Call Tree:`, then nothing. That reads like truncated output, or like idle time, but the stretch was a busy one: the markers show a keypress being dispatched.

Workaround: `thread samples` on the same zoom, and the `DOMEvent`/`Runnable` markers to see what ran.
