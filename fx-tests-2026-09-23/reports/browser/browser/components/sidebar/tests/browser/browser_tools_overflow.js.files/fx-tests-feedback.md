## Question: were these failures on revisions pushed before or after the fix landed?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_tools_overflow.js --task-ids --limit 0 --since 18`
- Expected: each task ID with its tree and revision (ideally push date), so that 28 timeouts run on 2026-09-14, days after the fix, can be told apart from a new regression.
- Got: task IDs grouped by run day only. The Sep 14 cluster looked like a fresh regression.
- Workaround: 28 `fx-tests task <id>` calls to get each revision, then the Treeherder push API per revision: all were backfill jobs on autoland pushes of 2026-08-14/15, predating the fix. A revision/push-date column (or grouping by push date) would have answered it directly.

## `--task-ids` chunk labels disagree with `fx-tests task`

- Command: `fx-tests test <path> --task-ids --limit 0 --since 9` (and `--json`, field `chunk`)
- Expected: the job's chunk.
- Got: `SqjE3rcdTyyg9fn2n0K1hg` listed as `...-chrome-1` (chunk 1), `BMFOuIyvTPqzBQnP3eMo7g` with no chunk (null), while `fx-tests task` reports both as `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`. All 28 of that day's tasks are chunk 7 per `fx-tests task`.
- Workaround: trust `fx-tests task`.

## `--since` refuses a date

- Command: `fx-tests test <path> --task-ids --since 2026-09-04`
- Expected: failures since that date (the day a fix landed).
- Got: `--since expects a non-negative integer`.
- Workaround: computed the day count by hand (`--since 18`).
