## Question: did any failure happen on a revision that contains a given fix?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_openShortcutSettings.js --history` and `--task-ids --limit 0`
- Expected: a way to tell run date from revision date. `--history` shows 3 failures on 2026-09-14, ten days after the fix (bug 1378104) landed, which reads as "still failing".
- Got: dates are run dates only, and `--task-ids` lists task IDs without their revision. The 2026-09-14 failures turned out to be backfills of autoland revisions from 2026-08-14/15 (a78ff5819100, da79ac063c12, 54cd4c62c282), which predate the fix.
- Workaround: `fx-tests task <id>` for each of 17 tasks to get the revision, then `curl` hg `json-rev` for the push date and `raw-file` to check whether the fix is in the tree.
- What would have answered it: the revision (and its push date) next to each task ID in `--task-ids`, or a `--history` mode bucketed by push date rather than run date.
