## Which of a list of tests have any failure data, in CI or on a try push (rows 700-1061, 2026-09-22)

- Commands: `fx-tests issues --harness mochitest --path devtools/ --type fail --type timeout --type crash --group-by test --limit 0 --json`, `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --limit 0 --json`, and `fx-tests test <path> --json` run 362 times as a cross-check.
- Expected: one command taking a list of test paths (or a file of them) and answering, per test, CI failures over the window, failures on a given try push, and whether a failure profile exists.
- Got: three outputs joined by a script; `test` takes one path per call (fast, 0.4 s each, so the loop was cheap).
- Could have shown: `fx-tests test <path>... --try <rev>`, or `fx-tests try <rev> --test` accepting several paths and listing the ones with no failure.

## Which failed try job had no test-level failure, and why

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --json` (and the text output).
- Expected: the task ID and job name of the "1 failed test jobs had no test-level failure attributed" job.
- Got: only `unblamedJobCount: 1`. Workaround: Treeherder jobs API for the push, diffed against every `taskIds` entry, then `fx-tests task KhHL-KXwRCODuYlPMTQ7eg` (says only "read the log") and the job's `bug_suggestions` and log: an ASan Glean `MOZ_CRASH(No database found)` in a background-task process.
- Could have shown: the unblamed job's task ID in the JSON and text, and in `task`, the error lines from Treeherder's bug suggestions with the test running at the time.
