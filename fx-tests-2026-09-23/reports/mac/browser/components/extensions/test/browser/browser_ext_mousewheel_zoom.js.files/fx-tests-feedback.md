## Question: "are the recent failures on current code, or retriggers of old pushes"

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_mousewheel_zoom.js --history`, then `--task-ids --limit 0`
- Expected: a way to see that the 3 failures listed on 2026-09-14 ran on revisions pushed on 2026-08-15 (before the fix that landed on 2026-09-04), i.e. that the test had stopped failing on current code.
- Got: `--history` buckets failures by the day the job ran, and `fx-tests task` prints the revision but not its push date, so the 2026-09-14 row reads as a recurrence after a 9-day gap.
- Workaround: the Treeherder push API (`/api/project/autoland/push/?revision=<rev>`) for each revision. A push date next to each task ID, or flagging jobs whose revision is much older than the run date, would have answered it.
