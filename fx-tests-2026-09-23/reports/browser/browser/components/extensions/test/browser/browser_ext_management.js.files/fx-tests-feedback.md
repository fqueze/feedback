## Question: "were these failures on revisions pushed before or after a given landing?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <taskId>` per task for its revision, then the Treeherder `/api/project/<repo>/push/?revision=` endpoint per revision for the push time.
- Expected: the date of each failing task's push next to its task ID, to see that the last failures precede a fix landing and that the 2026-09-14 ones are backfills of 2026-08-15 pushes.
- Got: task IDs grouped by the day the job ran, with no revision or push date; 16 extra calls to get them (hg.mozilla.org json-pushes returned nothing from this machine).
- What would have answered it: the revision and its push date on each `--task-ids` row, or grouping by push date rather than run date.

## Question: "which test in this job failed first?"

- Command: `fx-tests task <taskId> --messages --limit 0`.
- Expected: failing tests in execution order, so the first one in a cascade (the leaker) is on top.
- Got: an order that is neither alphabetical nor chronological in Xh6MnwM2R3OFkWAeuPuZLA (browser_ext_incognito_views.js listed before browser_ext_commands_execute_page_action.js); the resource-usage or per-test profile was needed for the order.
- What would have answered it: the start time of each failing execution on its row, or execution order.
