## The failing tasks of a test carry no revision, so "did every failure predate the fix?" takes one call per task

- Question: were all 33 failures on pushes from before the fix landed (2026-09-04 15:41 UTC), including the 3 run on 2026-09-14?
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tab_runtimeConnect.js --task-ids --limit 0` (and `--json`: `taskIds[]` has taskId, day, jobName, chunk, no revision)
- Expected: the revision (ideally its push date) next to each task ID, since a date of run is not the date of the code.
- Got: only the run date. Needed a loop of 33 `fx-tests task <id>` for the revision, then the Treeherder push API for the push time: the 3 runs of 2026-09-14 were on pushes of 2026-08-15.

## `test --task-ids` and `task` disagree on the chunk

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tab_runtimeConnect.js --task-ids --limit 0` lists `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` and `VYlSTop-RXKVXW_jxzuPVw.0  ...-chrome-1`
- `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA` and `fx-tests task VYlSTop-RXKVXW_jxzuPVw` both print `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`.
- Expected: the same chunk in both. Not investigated which one is right.
