## Question: were the failures after a fix landed on revisions that have the fix?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar_glean.js --history` and `--task-ids --limit 0`
- Expected: some way to see the pushed revision (or its push date) of each failing task, or `--history` bucketed by push date.
- Got: `--history` and `--task-ids` bucket by the day the task ran. 14 failures on 2026-09-14 read as a recurrence twelve days after the fix, but all 14 tasks were created 2026-09-14T07:20-07:24Z on autoland revisions pushed 2026-08-14/15 (backfills). Same for the 2026-09-02 19:05Z tasks, on revisions pushed 2026-08-27. `--json` taskIds have `day` but no revision.
- Workaround: for each of 26 task IDs, `curl .../api/queue/v1/task/<id>` for `GECKO_HEAD_REV`, then hg `json-pushes?changeset=<rev>` for the push date, then fetch the file at that revision to check for the fix. Took one background command over the 120 s timeout.
- What would have answered it: the revision (and push date) next to each task ID in `--task-ids`, and a note in `--history` when a day's failures are on revisions pushed earlier than a given date.

## `--bugs` printed nothing about bugs

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar_glean.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`; no line about bugs at all, so "no bug" and "flag ignored" look identical.
- Workaround: found the fixing bug (bug 2038933, filed on the preceding test) from `git log` of the test files.
