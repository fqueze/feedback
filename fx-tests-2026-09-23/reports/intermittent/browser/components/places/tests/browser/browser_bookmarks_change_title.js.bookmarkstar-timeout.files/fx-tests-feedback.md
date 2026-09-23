## Question: how many failures, per config, does one failure mode of a test have?

- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarks_change_title.js --task-ids --limit 0`
- Expected: the failing jobs grouped by failure mode (here: macOS `test_change_title_from_Toolbar` timeouts vs Linux/Windows `test_change_title_from_BookmarkStar` timeouts after `PageActions: No anchor node for bookmark`), with counts per config and per day.
- Got: one `TIMEOUT Test exceeded time limit` issue for both; the split had to be made by config from the task list, with a Python script over the saved output.
- Could show: the subtest the timeout hit and the last error logged before it, as the issue key, so `--issue <n>` selects one mode.
