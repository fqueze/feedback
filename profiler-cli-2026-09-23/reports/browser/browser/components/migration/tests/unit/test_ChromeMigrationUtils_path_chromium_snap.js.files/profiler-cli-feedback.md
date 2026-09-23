## Question: did two tests overlap in time, in a long resource-usage profile?

- Command: `profiler-cli marker info m-1 m-2 --session browser-snap-ru1` on a 14 min xpcshell resource-usage profile (task cA1Pn-roRHud0ZsRGqwYhQ).
- Expected: start/end precise enough to compare two ~400 ms `test` markers, and to place a per-test profile's 10 ms window inside them.
- Got: `Time: 1m54s - 1m54s (452ms)` for both markers; `thread markers --list` also prints `t=1m54s` for every row. Sub-second order is lost.
- Workaround: `marker info --json` and read `start`/`end` (113654.977 .. 114106.977 ms). Printing milliseconds (e.g. `1m53.655s`) when durations are sub-second would have answered it.

## Question: is this handle the marker a link's `marker=N` points to? (review)

- Command: `profiler-cli marker info m-13 --session browser-review-chromium_snap-1`, after loading a link with `marker=3164`.
- Expected: the marker's index (the number links use) somewhere in the plain output.
- Got: the name, time, and fields, but no index. I had to run `marker info m-13 m-10 --json` through a Python one-liner to read `markerIndex` for every marker I checked, in 4 profiles.
- Would have answered it: printing `Index: 3164` in plain `marker info`, or having `load <link>` print which m-handle `marker=N` resolved to.
