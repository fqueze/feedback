## Question: which subtest timed out, and does it differ per config?

- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarks_change_title.js` (and `--task-ids`, and `fx-tests task KFH32s6KQCSoQxf9eIzVRQ --profiles --messages`)
- Expected: the failure modes split by subtest — here macOS times out in `test_change_title_from_Toolbar` (238 jobs) while Linux/Windows time out in `test_change_title_from_BookmarkStar` after `PageActions: No anchor node for bookmark` (41 jobs).
- Got: a single issue `TIMEOUT Test exceeded time limit` (354x), and `--messages` for a task prints only `1x Test timed out`, without the subtest name or the preceding error.
- Workaround: loaded a per-test profile per config and read the Test markers (`Entering test ...`, console errors).
- Could show: the subtest the timeout hit (last `Entering test` before the timeout) and the last error logged before it, per task and grouped under Issues.
