## `try --test` prints no task IDs

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test toolkit/content/tests/chrome/test_tooltip.xhtml --task-ids` (and with `--json`)
- Expected: the task IDs of the job runs in the per-config table, since `--task-ids` was passed.
- Got: only `configuration / jobs / passed / passed on retry / failed`; no task IDs in text or JSON. It also says 3 jobs where the full report says "4 job runs ... 1 run not read", without mentioning the unread one.
- Workaround: `fx-tests try <rev> --all-jobs --task-ids --profiles --limit 0 --json` and filter `permaFails[]` by path.

## A timeout is reported as "No checks actually run"

- Command: `fx-tests task Vk8RoYlOT5CDUEz527I46A --messages --full-messages` (same in `fx-tests try ... --messages`)
- Expected: the test's row to say TIMEOUT / "Test timed out.", as the job's Outcomes line counts TIMEOUTs.
- Got: `FAIL — 2 failing executions of 2` with only `2x [SimpleTest.finish()] No checks actually run.` The timeout marker has an empty Message and "Test timed out." in its Subtest field, so it is dropped; the failure mode looks like a harness complaint rather than a hang.
- Workaround: load the per-test profile and read the TEST-UNEXPECTED-FAIL markers' Subtest field.
