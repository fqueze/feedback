## Which of a test's failures are in which subtest / carry which message

- Command: `fx-tests test browser/components/places/tests/browser/browser_bookmarks_change_title.js --task-ids --limit 0` (also `--exclude-config macosx`)
- Expected: the Issues block to split the 354 timeouts by subtest (`test_change_title_from_BookmarkStar` vs `test_change_title_from_Toolbar`) or by the error logged before them (`PageActions: No anchor node for bookmark`), so each failure mode has its task IDs.
- Got: one row, `354x TIMEOUT Test exceeded time limit`; `fx-tests task <id>` also only says `Test timed out`.
- Workaround: split by platform (`--exclude-config macosx`) on the sibling report's word, then load per-test profiles one at a time to read the `TEST-UNEXPECTED-FAIL` marker's `subtest`.
- Question the default output could have answered: "the task IDs of this test's timeouts in subtest X".
