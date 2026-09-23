# fx-tests feedback (test_suppressed_events_nested_iframe.html)

## `fx-tests test` has no failure message for any of the 40 failures, `fx-tests task` has it

- Command: `fx-tests test dom/base/test/test_suppressed_events_nested_iframe.html`
- Expected: the failure modes under Issues, e.g. `Test timed out.` /
  `[SimpleTest.finish()] No checks actually run.`
- Got: `40x FAIL Failure details not recorded (likely Android or platform logging issue)` —
  on Linux and Windows desktop jobs.
- Workaround: `fx-tests task <id>` on each of the 40 task ids (a shell loop, ~40 calls) to
  confirm they all fail the same way (they do: `No checks actually run.`, i.e. a timeout).
- Question it blocked: "do all failures of this test share one failure mode".

## The per-test line in `fx-tests task` omits "Passed when the harness reran it" for some jobs

- Command: `fx-tests task L9JQudOORgijyLJGvLbpWA --profiles` (also L8No3s_aSWGZGuzWZMOiAg)
- Got: the test's own entry says `FAIL — 1 failing execution of 2` without
  `Passed when the harness reran it.`, while the section header says
  `All 2 passed when the harness reran them.` The two statements look contradictory.
- Question it blocked: "did the retry pass in this job" (whether a `-2` profile should exist).

## The failing message shown is the fallout, not the first failure

- `fx-tests task` shows `[SimpleTest.finish()] No checks actually run.` The first
  `TEST-UNEXPECTED-FAIL` in the job is `Test timed out.` (subtest of the same test, 1 s
  earlier), which is what says "timeout". Showing the first one would name the failure mode.
