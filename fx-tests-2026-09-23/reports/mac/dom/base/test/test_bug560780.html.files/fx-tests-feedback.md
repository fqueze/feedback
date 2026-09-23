## `fx-tests test` Issues block has no failure message for a mochitest-plain test

- Command: `fx-tests test dom/base/test/test_bug560780.html`
- Expected: the failure modes under Issues, e.g. `Test timed out.` / `[SimpleTest.finish()] No checks actually run...`.
- Got: `41x FAIL Failure details not recorded (likely Android or platform logging issue)` — all 41 failures are macOS/Linux desktop runs, and `fx-tests task <id>` shows the message for each of them.
- Workaround: `fx-tests task <taskId> --messages` on each failing task.

## Question: "which manifest ran right before this test's manifest, in each job, and did the test pass there?"

- Needed to tell whether the failure follows a specific preceding manifest (a cross-browser leak: the manifest run by the previous browser instance) across passing and failing jobs, before and after a landing.
- Commands: Treeherder `/api/jobs/?push_id=` per push to list the passing jobs, then `fx-tests task <id> --json` and jq over `.passed[].path`.
- Got: `.passed` is sorted by test path, not in execution order, so the manifest order had to be inferred from path sorting; `fx-tests test --task-ids` lists failing tasks only, so passing jobs of the same chunk layout cannot be found from fx-tests.
- What would have answered it: `fx-tests test <path> --neighbours` (or `task`) listing, per job, the previous manifest in execution order and the outcome of the test, for passing jobs too.
