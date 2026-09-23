# fx-tests feedback (browser_ext_runtime_getContexts.js)

## `--history` dates failures by job run date, so backfills of old pushes look like a recurrence

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_runtime_getContexts.js --history` and `--task-ids --limit 0`.
- Expected: a way to tell that the 3 failures listed under 2026-09-14 are on revisions pushed on 2026-08-15 (a78ff5819100, da79ac063c12, 54cd4c62c282), i.e. backfills from before the fix that landed on 2026-09-04.
- Got: they show as 2026-09-14 failures, which reads as "still failing 10 days after the fix". The revision is only shown by `fx-tests task <id>`, one call per task, and the push date not at all.
- Workaround: `fx-tests task` per task for the revision, then `curl https://treeherder.mozilla.org/api/project/autoland/push/?revision=<rev>` for the push timestamp.
- Question it was for: "did it fail on any push that includes the fix?" A push date (or a "backfill of an older push" flag) next to each task in `--task-ids`, or a history keyed by push date, would answer it.
