## Marker times in `thread markers --list` and `marker info` are rounded to the second on long profiles

- Command: `profiler-cli thread markers --session <s> --search "external_links,privacy-logins,tabs-browsing" --list --limit 0` on a 22-minute resource-usage profile.
- Expected: millisecond precision (e.g. `t=18m18.358s`), since the question was the order of markers 50 ms apart (tab removed, then a JS error from the closed page).
- Got: every row `t=18m18s`; `marker info` likewise prints `Time: 18m18s - 18m46s (27.597s)` (the duration keeps ms, the start does not).
- Workaround: `--json` and reading `flatMarkers[].start`.

## `zoom push` with an out-of-profile range succeeds silently (review-browser_passwords_sidebar.js)

- Command: `profiler-cli zoom push 1161300,1162300 --session review-browser_passwords_sidebar.js-2` (I passed milliseconds, as `--json` prints `start`, instead of seconds), then `thread markers --list --limit 0 --json`.
- Expected: an error or warning that the range lies past the profile end (the profile is 20m43s long, so 1161300 s is far outside it).
- Got: the zoom was accepted and the marker list came back empty, with no warning. Since `--json` gives `start` in ms and `zoom push` takes seconds, this mix-up is easy to make.
- Workaround: divide by 1000 (`zoom push 1161.3,1162.3`).
