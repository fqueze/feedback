## Question: "did this test really fail again on 2026-09-14, or was that an old push?"

- Command: `fx-tests test <path> --history`, then `fx-tests test <path> --task-ids --limit 0`.
- Expected: some way to tell a recurrence apart from jobs retriggered or backfilled on old pushes.
- Got: the three failures dated 2026-09-14 look like a recurrence 10 days after a fix, but all three ran on autoland pushes from 2026-08-15. I found this only by running `hg json-pushes?changeset=<rev>` on each revision `fx-tests task` printed.
- What would have answered it: the push date next to each task in `--task-ids`, or a warning in `--history` when a day's failures are on pushes older than the window's fix point.

## Question: "which test failed first in this job?"

- Command: `fx-tests task <taskId> --profiles --limit 0 --messages`.
- Expected: the failing tests in execution order, or the first one flagged, since in a cascade the first failure is the leaker.
- Got: the list is sorted by path, so the leaker (`browser_ext_commands_execute_page_action.js`) sits among 44 victims, and only the resource-usage profile gave the order. Over 31 jobs, I scripted over the saved outputs to find the test with the distinct message.
- What would have answered it: execution order (or a "first failure in this job" line), and for a cascade, a note like "44 tests fail with the same message after X".

## `--task-ids` chunk names disagree with `fx-tests task`

- `fx-tests test <path> --task-ids` lists `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` and `VYlSTop-RXKVXW_jxzuPVw.0 ... -1`. `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA` says `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`, and so does the one for VYlSTop. No time lost, but one of the two is wrong.
