## `--bugs` prints no bugs section at all

- Command: `COLUMNS=250 fx-tests test netwerk/test/browser/browser_103_cleanup.js --bugs`
- Expected: a list of the sheriff-annotated bugs naming the test. Bug 1829935 ("Frequent netwerk/test/browser/browser_103_cleanup.js | single tracking bug") has 283 trunk annotations from 2026-08-14 to 2026-08-31.
- Got: the normal `fx-tests test` output with no bugs section and no line saying none were found. Maybe the lookup is limited to the last 7 days, which have no trunk annotations, but the output does not say so.
- Workaround: read the bug number from the test's hg log, then run `fx-tests intermittent --bug 1829935 --since 40 --tree all`.

## Question: "did every failure happen before the fix landed?"

- Command: `fx-tests test <path> --task-ids --limit 0`
- It gives task IDs grouped by day, with no revision and no push or creation time. To compare them with a fix's autoland push (11:43 UTC on the same day), I had to query each task from the Taskcluster queue API with a script (`payload.env.GECKO_HEAD_REV`, `created`).
- Could show: the revision (and repo) and push time per task ID. Also, `--history` could mark the first day after a landing that changed the test.
