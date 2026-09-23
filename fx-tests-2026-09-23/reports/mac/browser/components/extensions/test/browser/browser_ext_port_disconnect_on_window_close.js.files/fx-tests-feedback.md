## Question: did every failure run on a revision older than the landing of fix X?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <taskId>` for each task to read its revision, then a script that maps each hg revision to git through `https://lando.moz.tools/api/hg2git/firefox/<rev>` and runs `git merge-base --is-ancestor <fix> <git rev>`.
- Expected: `--task-ids` to show each task's repo and revision, or better, a `--since-rev <rev>` / `--after-landing <rev>` filter that counts failures on revisions that include a given commit.
- Got: `--task-ids` lists task IDs grouped by the date the job ran, with no revision. The dates misled here: the 3 failures dated 2026-09-14 ran on autoland revisions from 2026-08-15 (a backfill), so the per-day history looks like the failure came back 10 days after the fix landed when it did not.
- Workaround: one `fx-tests task` call per task (13 calls, slow), plus lando hg2git and git ancestry checks.
- What the output could show: the revision (and its push date) next to each task ID in `--task-ids`, and in `--history` a flag for runs whose revision is much older than the job date.

## `--bugs` with no annotated bugs prints nothing

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_port_disconnect_on_window_close.js --bugs`
- Expected: a line such as `Bugs: none annotated` so it is clear the lookup ran.
- Got: output identical to the run without `--bugs`. Only `--json` showed `"annotatedBugs": []`.
- Workaround: re-ran with `--json`.
