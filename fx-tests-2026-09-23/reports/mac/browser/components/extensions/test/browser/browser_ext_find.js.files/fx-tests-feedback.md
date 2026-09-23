## Did it fail on any push made after the fix landed? (browser_ext_find.js)

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_find.js --history` and `--task-ids --limit 0`
- Expected: a way to tell a failure on a recent push from a failure on an old push retriggered or backfilled later.
- Got: dates are task dates only. Three failures dated 2026-09-14 made it look as if the fix of 2026-09-04 had not held. They ran on autoland pushes of 2026-08-15.
- Workaround: `fx-tests task <id>` for each task to get its revision, then `hg json-pushes?changeset=<rev>` for its push date.
- What would have answered it: the revision and its push date in `--task-ids` rows, or `--history` bucketed by push date.

## Does it only fail when another test failed before it in the same job?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_find.js`
- Expected: some hint that all 45 failures are in jobs where `browser_ext_commands_execute_page_action.js`, which runs four tests earlier in the same manifest, also failed.
- Got: a plain "intermittent" verdict. Seeing the cascade took `fx-tests task` on each of 7 jobs, and matching the two tests' `--history` counts by hand.
- What would have answered it: "N of M failing jobs also failed <test> earlier in the same manifest", in `fx-tests test`.
