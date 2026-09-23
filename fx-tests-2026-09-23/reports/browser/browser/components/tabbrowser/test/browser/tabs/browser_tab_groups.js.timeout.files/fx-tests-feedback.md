## (review) Question: which subtest timed out, in each job of a TIMEOUT issue

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups.js --task-ids --limit 0 --issue 2`
- Wanted: for the 21 `Test exceeded time limit` jobs, the subtest each timed out in (they split 14 `test_groupHasActiveTab` / 7 `test_moveTabBetweenGroups`).
- Got: the task ids only; the message has no subtest, so one issue hid two hang sites.
- Workaround: downloaded all 21 `live_backing.log`s and grepped `| <subtest> - Test timed out`. Showing the subtest (the log line has it) next to each task id, or splitting the issue by it, would answer it.
