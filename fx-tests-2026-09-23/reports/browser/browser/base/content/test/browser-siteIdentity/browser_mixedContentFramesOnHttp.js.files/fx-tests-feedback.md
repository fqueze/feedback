## The push each failing job ran on

- Question: which autoland pushes did the 73 failing jobs of a burst run on, to bracket the landing that caused it and the one that stopped it.
- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_mixedContentFramesOnHttp.js --task-ids --limit 0` (and its `--json`).
- Expected: the revision (or push id) next to each task id, since the list is already grouped by day.
- Got: task id, job name, day, occurrences; no revision. `fx-tests task <id>` has it, but that is one call per task.
- Workaround: `curl` of `queue/v1/task/<id>` for each of the 73 tasks, reading `payload.env.GECKO_HEAD_REV`. A per-revision count of failing jobs would have answered it directly.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_mixedContentFramesOnHttp.js --bugs`
- Expected: a Bugs section, or a line saying no sheriff-annotated bug names the test.
- Got: the same output as without `--bugs`; nothing says whether the lookup ran or found nothing.
- Workaround: none; I assumed none was found.
