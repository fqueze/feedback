## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartformfill_autocomplete.js --bugs`
- Expected: a line such as "Annotated bugs: none" so that the absence is an answer.
- Got: the same output as without `--bugs`; no section at all. Only `--json` shows `"annotatedBugs": []`.
- Workaround: `--json | jq .annotatedBugs`.

## Question: which pushes (revisions) are behind a burst of failures?

- Command: `fx-tests test <path> --task-ids --limit 0` (and its `--json`).
- Question: 41 jobs failed the same way on 2026-09-02 only; which pushes, to find the landing and the backout.
- Got: task IDs, job names and day, but no revision or push. `fx-tests task <id>` shows the revision, one task at a time.
- Workaround: a loop over the Taskcluster queue API (`payload.env.GECKO_HEAD_REV`), then Treeherder's push API for push times (hg.mozilla.org `json-pushes` answered HTTP 406 after a few requests).
- What would have answered it: a revision (and push time) column in the `--task-ids` list.

