## Question: "which message is behind these failures?" (bug 2070301, browser_preselect_login.js)
- Command: `fx-tests test toolkit/components/passwordmgr/test/browser/browser_preselect_login.js`
- Expected: an Issues row for `TEST-UNEXPECTED-PASS ... We expect at least one assertion to fail because this test file is marked as fail-if in the manifest.` (54 of the 61 failures).
- Got: the same failure split into `profile uploaded in profile_browser_preselect_login.js.json` (24x), `...-2.js.json` (24x), and `-3` .. `-8` (1x each). The real message never appears, so the failure mode is only identifiable by reading the bug or a profile.
- Workaround: read the bug's comment 0 and the profile's TEST-UNEXPECTED-PASS marker.

## `--task-ids --limit 0` repeats rows
- Command: `fx-tests test <path> --task-ids --limit 0`
- Got: the 2026-09-08 task list printed twice in full, and `atTw29nVRmiX-nIwAKhkvA.0` listed 7 more times on its own lines, where other rows use `×2`.
- Expected: one row per job with a `×N` count.

## `--bugs` prints nothing when there is nothing
- Command: `fx-tests test <path> --bugs`
- Got: output identical to without `--bugs`; `--json` shows `annotatedBugs: []`.
- Expected: a line saying no sheriff-annotated bug names this test (bug 2070301 does, but has no annotations in window, per `fx-tests intermittent --bug 2070301`).

## Question: "did every failure come from a revision before the fix landed?"
- Command: `fx-tests task <taskId>` for each of 24 jobs, then Treeherder `/api/project/autoland/push/?revision=` for each revision's push time.
- `fx-tests task` gives the revision but not the push id/time, and `test --task-ids` gives neither; with them, "all failures fall between push A and push B" would have been one command.
