## Question: which revision, and pushed when, did each failing run test?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_reload_manifest_cache.js --task-ids --limit 0` (and `--json`)
- Expected: for each failing task, its repo, revision and push date, or at least a flag when the run is much later than its push (retrigger/backfill).
- Got: only the run day. The three 2026-09-14 failures look like failures after the fix that landed on 2026-09-04. They are backfills of autoland pushes from 2026-08-15 (a78ff5819100, da79ac063c12, 54cd4c62c282), which predate the fix. `--history` shows the same misleading 2026-09-14 blip. Only `fx-tests task <id>` prints the revision, one task at a time, with no push date.
- Workaround: a script that reads `payload.env.GECKO_HEAD_REV` from each task's Taskcluster definition, then asks Treeherder `/api/project/<repo>/push/?revision=<rev>` for `push_timestamp`. Output is in `task-revisions.txt`.
- What would have answered it: a revision column plus push date in `--task-ids`, and `--history` bucketed by push date, or at least marking runs whose push is more than a day older than the run.
