## The push each failing job ran on

- Question: which autoland pushes did the 69 failing jobs of a one-evening burst run on, to bracket the landing that caused it and the backout that stopped it.
- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_mcb_redirect.js --task-ids --limit 0`
- Expected: the revision (or push) next to each task id, or a per-revision count of failing jobs.
- Got: task id, job name, day and occurrence count; no revision. `fx-tests task <id>` has it, but that is one call per task.
- Workaround: `curl queue/v1/task/<id>` for each of the 69 tasks, reading `payload.env.GECKO_HEAD_REV`, then Treeherder's push API for each revision.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_mcb_redirect.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`.
- Workaround: none; I assumed none was found (the causing bug came from the backout commit message).
