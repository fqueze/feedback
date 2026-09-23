## A TEST-KNOWN-FAIL (todo) is reported as the failure message

- Command: `fx-tests test dom/events/test/test_mouse_events_after_touchend.html` (and `--task-ids`, `fx-tests task B3ErZi1EQ-6hiZie7ARnQw --profiles`).
- Expected: Issues to list the TEST-UNEXPECTED-FAIL messages ("Single tap should not cause mouse events if touchend is consumed ..." / "Multiple touch should not cause mouse events ...").
- Got: `174x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: ...` as the only issue, for every failing run. That line is SimpleTest's `todo()` from `requestFlakyTimeout`: status FAIL, expected FAIL (TEST-KNOWN-FAIL), so not a failure. It hides the real failure modes and their counts.
- Workaround: `fx-tests task <id> --messages --full-messages` per job, and `fx-tests intermittent --bug 2031660` for the split over the annotated jobs only.

## Question: "the failure modes of this test, with their counts"

- Because the Issues block showed only the TEST-KNOWN-FAIL line above, the real split had to be rebuilt by looping over the jobs: `for t in <37 task ids from fx-tests test <path> --task-ids --limit 0>; do fx-tests task $t --messages --full-messages --quiet | rg '<assertion names>'; done`, then `sort | uniq -c`.
- It could have shown: Issues rows per distinct TEST-UNEXPECTED-FAIL message (with the `got "..."` value cut or normalised), counted per execution. Here that is 28 × "Single tap should not cause mouse events if touchend is consumed", 15 × "Multiple touch should not cause mouse events" over 2026-09-16 … 09-20.
