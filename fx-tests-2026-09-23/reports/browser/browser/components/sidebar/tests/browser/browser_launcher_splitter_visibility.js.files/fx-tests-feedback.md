## Question: are these failures on current code, or on old revisions being backfilled?
- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_launcher_splitter_visibility.js --history` / `--task-ids`
- Expected: to see that the 09-02 and 09-14 failures ran on 08-15/08-27 revisions, before the 08-28 fix.
- Got: run dates only; needed 18 `fx-tests task <id>` calls, then Treeherder push API for push dates and lando hg2git + `git merge-base --is-ancestor` per revision (same question as logged in browser_syncedtabs_sidebar_glean.js.files/fx-tests-feedback.md).
- What the output could show: each failing task's revision and its push date in `--task-ids`, flagging revisions much older than the run date.
