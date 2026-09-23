# fx-tests feedback

## Question: a passing run of this test on another config, to compare with the failing one

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_chat_browser_tabbable.js --task-ids --limit 0`
- Expected: a way to get the task ID of a job where the test ran and passed on a given config
  (here linux2404-64 debug standalone, which passes 244/244, and windows11-64 debug non-standalone),
  ideally on the same push as a failing one.
- Got: only failing tasks; `--coverage` gives per-config counts but no task IDs.
- Workaround: Treeherder `api/project/autoland/push/?revision=` + `api/jobs/?push_id=` to list the
  push's jobs, then each task definition's `MOZHARNESS_TEST_PATHS` from the Taskcluster queue to find
  the chunk running `browser/components/aiwindow/ui/test/browser/browser.toml`. About 80 requests.
- Could have shown: `fx-tests test <path> --config <cfg> --passing-task-ids` (or `--task-ids --all`),
  or `fx-tests try/push <rev> --test <path>` listing every job of the push that ran the test with its outcome.

## Question: how many runs failed the way I diagnosed

- Command: `fx-tests test <path>`
- Got: the summary says `440 fail ... 1 crash`, but the per-config table counts that crash as a fail
  (`test-windows11-32-25h2/opt-mochitest-browser-chrome  0.1%  1`), so the table sums to 441.
- Could have shown: crashes in their own column, or the same counting in both places.
