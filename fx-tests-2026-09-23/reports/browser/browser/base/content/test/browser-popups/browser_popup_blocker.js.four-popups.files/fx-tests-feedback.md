## Question: "which subtest emitted this failure, in each failing run?"

- Command: `fx-tests test browser/base/content/test/popups/browser_popup_blocker.js --task-ids --limit 0 --issue 4` (and the same for the new path), then `fx-tests task <id> --messages` for each of the 19 tasks.
- Expected: the failure mode under Issues, or the task list, split by subtest. The raw log line has it (`browser_popup_blocker.js | test_bug2025170_unblock_popup - Uncaught exception ...`), and `fx-tests intermittent --bug` does show the subtest.
- Got: the message without its subtest. Two subtests (`test_bug2025170_allow_all`, `test_bug2025170_unblock_popup`) emit the identical message, and the brief I was given assumed all 20 runs came from one of them. They split 10/10.
- Workaround: 19 `fx-tests task <id> --messages` calls, inferring the subtest from the message that follows (`Test timed out` versus `Found an unexpected popup`), then confirming in 4 profiles.
- What would have answered it: a subtest column in `--task-ids`, or grouping Issues by `<subtest> - <message>`.

## `task --messages` drops the "Passed when the harness reran it" line that `task --profiles` shows

- Command: `fx-tests task SFmo9aCKQ96rHSAAo5WL6w --messages` and `fx-tests task SFmo9aCKQ96rHSAAo5WL6w --profiles`
- Expected: the same retry verdict in both.
- Got: `--messages` shows `FAIL — 1 failing execution of 2` for browser_popup_blocker.js, with no "Passed when the harness reran it." line (the other tasks do show one). `--profiles` says "All 3 passed when the harness reran them."
- Workaround: read `--profiles` for the retry verdict.
