## `test --task-ids` prints wrong chunk numbers

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js --task-ids --limit 0`
- Expected: the job name with its chunk, as `fx-tests task` prints it.
- Got: many tasks listed as `test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` or with no chunk suffix (e.g. `SqjE3rcdTyyg9fn2n0K1hg`, `BMFOuIyvTPqzBQnP3eMo7g`), while `fx-tests task <id>` and the Taskcluster task name both say `...-browser-chrome-7`. All 28 mac tasks are chunk 7; the list reads as if the failure spanned 3 chunks.
- Workaround: fetched each task's `metadata.name` from the Taskcluster queue API.

## Question: "which revision (and push date) did each failing task run?"

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`: `taskIds[]` has no revision).
- Needed to see that all 28 mac failures on 2026-09-14 were retriggers (`action: retrigger-multiple-task`) of autoland pushes from 2026-08-14/15, not new code. The day column (the day the task ran) hides that completely; it looked like a new one-day regression.
- Workaround: loop over task ids with the Taskcluster queue API (`payload.env.GECKO_HEAD_REV`, `tags.action`), then Treeherder's push API for push dates. The output could show the revision, and flag a task whose revision was pushed long before it ran.

## Question: "every job on this revision that failed this test"

- Three other retriggers of the same push (autoland 169ddaae128b), `IIKPNwedRoS47snrlKqSbg`, `Ph5ICkzxT9WbRVSru68qIg`, `eRu-OeM9Rh6OgGY2WNzU5A` (created 2026-09-14 07:19), fail this test the same way (`fx-tests task` shows it), but are not in `fx-tests test <path> --task-ids --limit 0`, which lists only 2 tasks for that push. So the 28 count undercounts, and nothing says so.
- Workaround: listed the push's task group via the Taskcluster API and read the logs.
