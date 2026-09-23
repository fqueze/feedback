## Times after 60 s lose their milliseconds in text output

- Command: `profiler-cli thread markers --session browser-892956-1 --category Test --search browser_892956 --list --limit 0`, then `profiler-cli marker info m-10 m-13 --session browser-892956-1`
- Expected: timestamps precise enough to line markers up (as `t=18.552s` is before the one-minute mark).
- Got: `t=1m7s`, `t=1m14s`, and `Time: 1m7s (instant)` in `marker info`: seconds only, so two markers 0.3 ms apart and 900 ms apart print the same.
- Workaround: `--json` and read `start`. Printing `t=1m7.318s` would avoid it.

## Question: "over which spans did this observer keep the refresh driver ticking?"

- Command: `profiler-cli thread markers --session browser-892956-1 --search "Synthetic mouse move" --list --limit 0 --json`, then a Python script grouping consecutive markers less than 40 ms apart into runs.
- The zoomed summary's Frequency Analysis (interval min/avg/max) answers it for one known range, but finding the ranges needs the whole list (2,995 markers here). A `--runs <gap-ms>` grouping, printing start, end and count of each burst, would have answered it directly.

## Question: "which test was running at t?" in a per-test profile (review)

- Command: `profiler-cli thread markers --session browser-review-browser_892956_destroyWidget_defaultPlacements.js-1 --category Test --search 'Entering test ' --list --limit 0 --json`, then a script taking the first marker per `test` field.
- The profile has one `test` interval marker per test (`PASS — <path>`), which answers it directly, but `--search name:test` also matches every Log marker's `test` payload field (546 hits instead of ~26). A name-only filter (or a `profile tests` listing) would have given the timeline in one command.
