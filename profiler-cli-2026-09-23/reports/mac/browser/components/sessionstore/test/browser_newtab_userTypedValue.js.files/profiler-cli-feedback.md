## Marker times in `--list` and `marker info` are rounded to whole seconds past one minute

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-2565 m-19 m-2566 --session <s>`
- Expected: millisecond times (e.g. `t=113.466s`), since ordering markers within one second is the whole question for a race.
- Got: `t=1m53s` for every marker between 113.0 and 113.9 s; `marker info` prints `Time: 1m53s - 1m53s (23.743ms)`.
- Workaround: `marker info ... --json` and read `start`/`end` in a python one-liner.

## The same rounding in `profile markers` (cross-thread sweep)

- Command: `profiler-cli profile markers --search "about:blank" --session <s>`
- Question: did the content process finish loading about:blank before or after the parent started the restore (both within 113.7-113.8 s)?
- Got: `t=1m53s` / `t=1m54s` for every row, so the cross-process order could not be read.
- Workaround: `--json`, sort by `start`, print `start/1000` with 3 decimals (a small wrapper script).

## (review) Which marker does a link's `marker=N` point at?

- Question: checking a report's link means matching its `marker=N` (a marker index) to the marker the report quotes. That needs the index of each candidate marker.
- Command: `profiler-cli marker info m-14 m-2561 m-11 --session <s>`
- Expected: the marker index in the default output, or a way to look a marker up by index (e.g. `marker info --index 1730088`).
- Got: the default output has no index, so I had to use `--json` and a python one-liner over `markerIndex` for every batch. In the `--json` records, `handle` is also missing, so a multi-handle batch can only be matched back to handles by its order.
- Workaround: find each candidate with `thread markers --search`, then `marker info <handles> --json | python3 …` to print `markerIndex`, start and name.
