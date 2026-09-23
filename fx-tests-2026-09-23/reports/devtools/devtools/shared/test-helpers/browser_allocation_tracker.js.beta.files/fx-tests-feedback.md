## Failure rate of one test on a non-trunk tree (mozilla-beta DevEdition)

- Question: how often does `devtools/shared/test-helpers/browser_allocation_tracker.js` fail on mozilla-beta `linux2404-64-devedition` opt, per execution, and since when?
- Command: `fx-tests test devtools/shared/test-helpers/browser_allocation_tracker.js` (and `--history`)
- Expected: a beta row, or a `--tree` / `--data-source beta` option.
- Got: trunk only (`--data-source central|try|local`); DevEdition configs never appear. `fx-tests intermittent --bug 1767786` needed `--tree all --since 30` to show anything, and counts only sheriff-starred jobs, not runs.
- Workaround: Python over the Treeherder push/jobs API, then each job's `mochitest-devtools-chrome_errorsummary.log` (group result) and `live_backing.log` (first-run vs retry values): ~70 log downloads, about 15 minutes.
- What would have answered it: `fx-tests test <path> --tree mozilla-beta` with per-config pass/fail/retry counts and `--history`.

## Per-occurrence lines show only the retry's message

- Command: `fx-tests intermittent --bug 1767786 --tree all --since 30 --limit 0 --json` (`occurrenceRows[].lines`)
- Expected: the first run's failure too.
- Got: only the retry's lines (e.g. Xf3b8ZPPQnuyq5Ggg_xrqw shows `-36`, its first run had `-30`); the errorsummary artifact has the same limitation.
- Workaround: grep the live log.
