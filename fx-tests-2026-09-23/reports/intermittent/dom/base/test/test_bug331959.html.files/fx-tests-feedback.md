## `fx-tests test` shows "Failure details not recorded" for failures whose message `fx-tests task` knows

- Command: `fx-tests test dom/base/test/test_bug331959.html`
- Expected: the Issues section to show `[SimpleTest.finish()] No checks actually run. ...` (what `fx-tests task RvTP9NRFTcyoxaOUB4101w.0` shows for this test).
- Got: `36x FAIL Failure details not recorded (likely Android or platform logging issue)` for all 36 failures, on macOS.
- Workaround: `fx-tests task <id> --messages` on each task.

## `fx-tests test` does not see the failures sheriffs are starring now

- Command: `fx-tests test dom/base/test/test_bug331959.html` vs `fx-tests intermittent --bug 2031724`
- Expected: some trace of the 16 jobs annotated 2026-09-16..09-20 on macosx1500-aarch64-vms.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-plain  256 runs 256 pass 0 fail`, and 0 failures anywhere after 09-10.
  `fx-tests task` explains why for each (killed at maxRunTime, partial profile), but `fx-tests test` gives no hint that
  N jobs of this config were unreadable, so the verdict reads "stopped failing" while it is failing daily.
- Workaround: the per-test profiles of those tasks are uploaded anyway (`public/test_info/profile_test_bug331959.html.json`,
  and `-2` for the retry), found by listing the task's artifacts with the Taskcluster queue API.
  The question: "the per-test profiles of a job killed at maxRunTime" — `fx-tests task <id> --profiles` could list the
  `profile_*.json` artifacts even when the resource-usage profile is a partial stream.

## "job name" in `fx-tests test --task-ids` drops the chunk for backfills

- Command: `fx-tests test dom/base/test/test_bug331959.html --task-ids --limit 0`
- Got: 20 of the 2026-09-08 rows as `test-macosx1500-aarch64/opt-mochitest-plain` (no chunk), while `fx-tests task` names
  them `...-plain-3`. They are all `backfill-task` actions by one user within 20 minutes (task tags `action: backfill-task`),
  which is the whole 09-08 spike (27 of 36 failures). Nothing in `fx-tests test` or `--history` says so; I found it via
  the Taskcluster task definitions. A retrigger/backfill flag per row would have saved that detour.
