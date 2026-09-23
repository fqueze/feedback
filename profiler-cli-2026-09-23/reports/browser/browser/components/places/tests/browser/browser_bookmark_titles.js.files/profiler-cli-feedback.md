## Question: at which millisecond did each line of the test's log happen?

- Command: `profiler-cli thread markers --category Test --search browser_bookmark_titles --list --limit 0 --session browser-browser_bookmark_titles.js-1`
- Expected: a timestamp precise enough to order and space the test's assertions (ms), since the whole test runs in ~2.5 s.
- Got: `t=2m42s` / `t=2m43s` / `t=2m44s` for all 26 lines; 8 lines share `t=2m44s`. No flag changes the time format.
- Workaround: `--json` and a Python one-liner printing `start/1000` per marker. `marker info` also prints `Time: 2m44s`.
- What would have answered it: `--list` printing `t=164.248s` (ms) when the list spans less than a few minutes, or a `--precise-times` flag.

## Question: how long did each listed interval marker last, in JSON?

- Command: `profiler-cli thread markers --search places.sqlite --list --limit 0 --json`
- Expected: an `end` or `duration` field I could rely on for flat markers.
- Got: `duration` (not `end`); I first read `end`, got None for all, and printed 0 durations. The schema is fine, but the text `--list` already shows durations, so needing JSON here was only for the timestamps above.
