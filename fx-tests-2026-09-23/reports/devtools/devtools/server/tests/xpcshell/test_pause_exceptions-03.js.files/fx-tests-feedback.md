## `task` picks a different "first message" for the same failure (test_pause_exceptions-03.js, 2026-09-22)

- Command: `fx-tests task <taskId> --profiles --limit 0 --full-messages` on the 6 msix tasks.
- Expected: the same first message for the same failure mode.
- Got: 5 tasks show `…test_pause_exceptions-03.js | Timed out and was force-killed by the harness…`; PyUxuOpdQj2b3T3XyQOKpg shows `Test timed out`. The profile holds both messages for this test in PyUxuO too, in the same order as in the other jobs (test marker `Message: Test timed out` at start, ERROR at the replay).
- Workaround: loaded the profile to check it was the same failure.
