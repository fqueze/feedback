## Question: was each failure on a revision before or after a given landing?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_openShortcutSettings.js --task-ids --limit 0` (and `--json`)
- Expected: the revision and push time of each failing task, so a fix landing can be checked against the last failure.
- Got: task IDs grouped by run date only. The run date misled: 3 failures dated 2026-09-14 were reruns of revisions pushed 2026-08-15, which made the test look like it still failed 10 days after the fix.
- Workaround: script over the Taskcluster task definition (`payload.env.GECKO_HEAD_REV`) plus `hg json-pushes?changeset=` per task.
- Could show: a revision / push date column in `--task-ids`, or flag rows whose revision is much older than the run date.

## Question: does the same other test fail first in every job where mine fails?

- Command: `fx-tests task <id>` run by hand on 7 tasks, then a script over `fx-tests task <id> --json` for all 41.
- Expected: a cross-job view of the tests failing alongside mine (count of jobs each co-fails in), which is how a cascade victim is recognised.
- Got: one job at a time.
- Could show: `fx-tests test <path> --co-failures` listing, per other test, in how many of this test's failing jobs it also failed, and whether it ran before.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test <path> --bugs`
- Expected: a Bugs section, or "no bug names this test".
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look identical.
