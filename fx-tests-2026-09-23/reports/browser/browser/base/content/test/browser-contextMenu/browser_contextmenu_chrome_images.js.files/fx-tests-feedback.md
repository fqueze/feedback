## Which subtest timed out, per task

- Command: `fx-tests task YY2w8RGjQeOlZ4PaUPyntg --messages`
- Expected: the `TEST-UNEXPECTED-FAIL | <file> | <subtest> - Test timed out` line, which names the subtest.
- Got: only `1x Test timed out`. The same holds for `fx-tests test <path>` Issues (`88x TIMEOUT Test exceeded time limit`). A test with several subtests can time out in any of them (here test_context_menu_items_for_allowed_chrome_images in 54 jobs and test_save_allowed_chrome_image in 18), and that decides which profiles to read.
- Workaround: `curl -sL --compressed .../public/logs/live_backing.log | grep -o '<file> | test_[a-z_]* - Test timed out'`, once per task.

## Did it stop on trunk while continuing on release branches

- Command: `fx-tests intermittent --bug 2013661 --since 30 --tree all --history`
- Expected: annotations per day, split by tree.
- Got: per-day totals across all trees, so the trunk stop on 09-04 is hidden by the esr153 annotations after it. The occurrence list has the tree per row, but it is truncated and not grouped.
- Workaround: a script over `--json` `occurrenceRows`, counting by (pushTime day, tree).
