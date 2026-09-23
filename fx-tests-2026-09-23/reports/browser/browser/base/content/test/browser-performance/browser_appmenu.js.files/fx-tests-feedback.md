## `fx-tests test` / `fx-tests task --messages` report a TEST-KNOWN-FAIL (todo) as the failure

- Command: `fx-tests test browser/base/content/test/performance/browser_appmenu.js` (and `fx-tests task f58k5Eo9RYKMgGOxjZx5Cw --messages`)
- Expected: the "Issues (first failure per run)" row and the per-task message list to show the TEST-UNEXPECTED-FAIL messages only.
- Got: issue 6 is `FAIL known reflow at openPopup/this._openPopupPromise<@... was encountered 1 times`, which is a `todo(false, ...)` (TEST-KNOWN-FAIL, marker `m-13` in the profile) logged before every run's real failure. The real failure (`unexpected 1 changed rects: ({x1:264, ...})`, `should have 0 unknown flickering areas`) only shows in `--messages`, and 61 runs all group under the todo text, so the failure mode's name is wrong for every one of them.
- Workaround: `fx-tests task <id> --messages` and read past the first line; confirmed with the profile's `TEST-KNOWN-FAIL` vs `TEST-UNEXPECTED-FAIL` markers.

## Failure first seen: no way to get the date/revision of the step change

- Question: "which push first failed, and what was the last passing push before it" (to bound a regressor).
- Command: `fx-tests test <path> --history` gives per-day counts; `--task-ids` gives task ids per day but not revisions or push times, so I had to run `fx-tests task <id>` on the first ids, then Treeherder's push API and lando's hg2git to get a date and git hash.
- Could have shown: the revision (and push time) next to each task id in `--task-ids`.
