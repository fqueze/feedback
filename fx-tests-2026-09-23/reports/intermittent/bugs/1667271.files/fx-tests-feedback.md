# fx-tests feedback (bug 1667271)

## Question: which manifest each annotated job failed on, for a harness-level bug with no TEST-UNEXPECTED-FAIL line

- Command: `fx-tests intermittent --bug 1667271 --since 21 --limit 0`
- Expected: some per-occurrence text telling the 72 jobs apart. Here, the line after "No tests were found for flavor 'plain' and the following manifest filters:" names the manifest (`pathprefix([...])`), and that splits the bug into its two failure modes.
- Got: "Tests named, per annotated job: (none: ... the API only keeps lines matching that marker)", and `lines: []` in `--json`.
- Workaround: downloaded all 72 `live_backing.log` files and grepped for the `pathprefix(` line.
- What would have answered it: for bugs whose summary is an ERROR line rather than a TEST-UNEXPECTED-FAIL, keep the error line plus the next one or two (or Treeherder's error-summary lines for the job).

## Question: how many of the annotated jobs are retriggers or backfills of one scheduling event

- Command: `fx-tests intermittent --bug 1667271 --since 21 --limit 0` (then `--json`, grouped by `pushTime`/`revision` with a script)
- Expected: a way to see that 55 of the 58 jobs on 2026-09-09/10 were one sheriff backfill (task tag `action: backfill-task`, Treeherder symbol `1-2f0b4d2b4cf-bk`), and 9 more were `retrigger-multiple-task` retriggers. That turned 72 "annotations" into 8 failures CI actually scheduled.
- Got: one row per job, sorted by push time, with the job name cut off (`geckoview-m…`). Nothing marked a job as a retrigger or backfill. `--history` showed a 36/22 spike on 2026-09-09/10, which looks like a real regression but is really the backfill.
- Workaround: fetched the 72 task definitions from the Taskcluster queue and read `tags.action` and `extra.treeherder.symbol`.
- What would have answered it: an `origin` column (decision / retrigger / backfill, from `tags.action`), and a count of distinct pushes next to the job count.

## `fx-tests test` refuses a manifest path

- Command: `fx-tests test dom/media/webspeech/recognition/test/mochitest.toml`
- Expected: the manifest's tests, grouped.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...mochitest.toml".
- Workaround: ran `fx-tests test <one test file from the manifest> --coverage`. That did answer "never runs on Android".
