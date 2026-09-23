## Marker times rounded to whole seconds inside a 3 s zoom (test_census_diff_04.js)

- Command: `profiler-cli zoom push 311.2,314.3 --session test_census_diff_04.js-1` then `profiler-cli thread markers --session test_census_diff_04.js-1 --list --limit 0`
- Expected: timestamps precise enough to order the ~60 log markers in a 3 s window (ms, as the view is 3.1 s long).
- Got: every row shows `t=5m11s` … `t=5m14s`, so the order of the harness's steps (last chmod, pidof checks, ps listing, exception) could not be read. `marker info m-1` also prints `Time: 5m11s - 5m14s (2.882s)`.
- Workaround: `--json` and read `start`. Scale the displayed precision to the zoom width.

## Question: "which processes changed name between two consecutive ps listings?"

- Command: `profiler-cli thread markers --session test_census_diff_04.js-1 --search get_process_list --list --limit 0 --json` (88 MB), then a script parsing each DEBUG `get_process_list: [[pid, name, user], …]` payload and diffing consecutive listings per pid.
- The default output shows the first ~200 characters of each listing, which are kernel threads; the app processes at the end are cut. What would have answered it: a way to show a long payload's tail or a `--search` hit in context (the matched substring with surrounding text) rather than the start of the label.
