## Issues block hides the mac failure message

- Command: `fx-tests test dom/base/test/test_bug1433073.html`
- Expected: the 21 mac failures under Issues with their message, `Test timed out.` / `[SimpleTest.finish()] No checks actually run. ...`.
- Got: `21x FAIL Failure details not recorded (likely Android or platform logging issue)`. Running `fx-tests task <id>` on each of the 21 tasks shows the message every time, so the details exist.
- Workaround: `fx-tests task` on every failing task (21 calls) to learn that they are one failure mode, and that `test_bug1100912.html` and `test_bug1101364.html` failed the same way in all 21 jobs.

## Question: which manifest ran just before this test's manifest, in failing and in passing jobs

- The question: does the failure depend on the manifest before `dom/base/test/mochitest-bug2.toml`, and did the trigger still occur after the fix landed (passing runs included)?
- Command: none answers it. I fetched `public/test_info/manifests.list` for 4,288 hardware mac opt mochitest-plain jobs (a job list another agent built from the Treeherder API) with curl, and joined the predecessor of `mochitest-bug2.toml` with the failing task IDs from `fx-tests test --task-ids`.
- What could have shown it: `fx-tests test <path> --predecessor` (or in `--coverage`/`--executions`): per run, the manifest that ran before the test's manifest in the same job, with pass/fail, so an order dependency across manifests shows as a table.
