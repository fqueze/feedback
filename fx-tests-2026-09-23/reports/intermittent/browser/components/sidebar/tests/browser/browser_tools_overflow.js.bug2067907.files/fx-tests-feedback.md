## Question: "which revision (and push date) did each failing task run, and was it a retrigger/backfill?"

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_tools_overflow.js --task-ids --limit 0 --since 18`
- Expected: enough to tell whether a post-fix failure cluster is a new regression or old revisions being re-run.
- Got: day + job name + task id only. The 2026-09-14 cluster (28 timeouts, one config) looked like a new regression after a fix; it was actually a burst of `retrigger: true` tasks run on autoland pushes from 2026-08-14/15, before the fix. `--history` also buckets by task run day, not push date, so the step change after a fix shows a false "blip".
- Workaround: scripted over `--json` taskIds, fetched each Taskcluster task definition (GECKO_HEAD_REV, created, tags.retrigger) and Treeherder push timestamps.
- Could have shown: revision (and its push date) per task id, a retrigger/backfill marker, and in `--history` optionally bucket by push date.

## Minor: `--since` takes a day count, not a date
- `fx-tests test <path> --since 2026-09-04` -> `--since expects a non-negative integer`. Accepting a date (as `--day` does) would help when the question is "since the fix landed".
