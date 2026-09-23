## Question: "which JS code made DOM changes during this window, and did function X ever do so"

- Command: `profiler-cli thread markers --session <s> --search SetNeedStyleFlush --list --limit 0` over the 88 s the test waited (5,435 markers)
- Expected: a way to group markers by their stack (e.g. `--group-by stack` on the top N JS frames), or to filter markers by a function in their stack (`--stack-search TickLabels`).
- Got: a flat list of 5,435 `SetNeedStyleFlush ✓` rows with no stack text. Answering needed a script: `--json` for the handles, then `marker info <200 handles> --json` in batches, then grouping the JS frames myself. It showed 5,076 of them came from `CurrentTimeScrubber.onCurrentTimeUpdated` and none from the tick-label rendering, which was the answer.
- Workaround: the script above (about 30 `marker info` calls).

## (review) Question: "at what time was the sample that is in function X"

- Command: `profiler-cli thread samples-bottom-up --session <s> --search updateTicks` over a 200 ms zoom
- Expected: the timestamp of each matching sample, e.g. `--list` on the samples commands, the way `thread markers --list` gives one row per marker. The question was whether `updateTicks` ran before or after a flush 2 ms earlier.
- Got: an aggregated tree with percentages and no times. Answering it took five narrowing `zoom push` + `samples-top-down --include-idle` calls, one per 1 ms slice.
- Workaround: the zoom narrowing described above.

## (review) `marker info --json` output has a different shape for one handle and for several

- Command: `profiler-cli marker info m-15 --session <s> --json`, compared with `marker info m-13 m-14 m-15 --json`
- Expected: the same shape both ways, for example always an array.
- Got: an object for one handle and an array for several, which broke the same script twice.
- Workaround: `d if isinstance(d, list) else [d]`.
