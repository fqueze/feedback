# fx-tests feedback

## Which subtest timed out is not in the task or test output
- Command: `fx-tests task <taskId> --profiles` (and `fx-tests test <path> --task-ids`) for the 6 failing jobs of `browser_uncontrollable_audio_focus.js`.
- Question: which subtest (add_task) timed out in each job? The test has three, and the failure mode differs by subtest.
- Got: `TIMEOUT — Test timed out` / `Test exceeded time limit` for every job, with no subtest name, although the harness log line and the TEST-UNEXPECTED-FAIL marker carry it (`test_html_media_vs_web_audio - Test timed out`).
- Workaround: load each per-test profile and read the `TEST-UNEXPECTED-FAIL` marker. The subtest could be shown next to the message, and grouped under Issues.

