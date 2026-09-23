## Question: "how many runs and failures of this test on one platform?"

- Command: `fx-tests test <path> --coverage --limit 0`, for both the old and the new path, then `grep linux | awk` to sum the runs and fails columns.
- The report needs a denominator for a platform-scoped failure mode (here: Linux only, 10 failures). The per-config table has it, but only summed by hand. A per-platform total line (runs, fails) under the `Scheduled on:` block would answer it.

## Question: "in which subtest, and after which assertion, did each timeout happen?"

- Command: `fx-tests task <id> --messages` gives only `1x Test timed out`, with no subtest name and no last passing assertion. For the tsan job, which has no per-test profile, I fetched `public/logs/live_backing.log` and grepped it.
- That is how I found that one of four timeouts (CTj93fTJRD25TgZrj6I3kA) happens at a later step than the other three, after `fullscreen should be restored to outer element` passed. The log line `TEST-FAIL | <path> | <subtest> - Test timed out` names the subtest. Showing that name, plus the last `TEST-PASS` before the timeout, would split timeouts by step without a download.
