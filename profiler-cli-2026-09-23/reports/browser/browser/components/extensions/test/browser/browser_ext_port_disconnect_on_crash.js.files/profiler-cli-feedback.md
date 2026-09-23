## Question: the exact time of a marker past the first minute
- Command: `profiler-cli thread markers --category Test --search <file> --list --limit 0`
- Expected: millisecond timestamps, needed to zoom into the vsync wait between `checking for open popups` and the failure.
- Got: `t=2m29s` / `t=2m35s`, rounded to the second once past 60 s.
- Workaround: `profiler-cli marker info m-A m-B --json` and reading `.start` through a Python script.

## Question: which marker a link's `marker=N` points to (review)
- Command: `profiler-cli thread markers --search PVsync --list --limit 0`, then `marker info <each handle> --json` to find `markerIndex` 824537.
- Expected: a way to go from a marker index to its handle (e.g. `marker info --index 824537`), or `markerIndex` shown in `--list --json` rows.
- Got: the index only in `marker info`; finding it among 321 same-named markers took a script over `marker info $(all handles) --json`.
- Workaround: that script.
