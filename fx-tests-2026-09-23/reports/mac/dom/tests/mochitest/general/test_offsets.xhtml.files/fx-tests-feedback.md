# fx-tests feedback (test_offsets.xhtml)

## Timeouts counted as FAIL, with a later message as the "first failure"

- Command: `fx-tests test dom/tests/mochitest/general/test_offsets.xhtml`
- Expected: 8 timeouts, Issue `Test timed out.`
- Got: `8 fail   0 timeout`; Issues `6x FAIL Failure details not recorded (likely Android or platform logging issue)` and `2x FAIL [SimpleTest.finish()] No checks actually run.`
- Reality (profiles and job log): every one of the 8 is a 300 s harness timeout. The first `TEST-UNEXPECTED-FAIL` of each run has an empty `message` and the text in `subtest` (`Test timed out.`); the `No checks actually run` comes 1 s later from `killTest`. For the `-cf` config the details are recorded too, same two lines.
- Workaround: read the profile's `TestStatus` markers.
- Suggestion: take the message from `subtest` when `message` is empty, and classify a `Test timed out.` subtest as a timeout.

## The job a confirm-failure job confirms is not reachable

- Question: "which job did this `-cf` job confirm, and did it fail too?"
- Command: `fx-tests task Z2RgmK1KTSClnXHDDEZnKA --profiles`
- Got: the CF job only. The confirmed job (M84Z3WoXQiqaB5w4ULDPEQ, killed at its 3600 s max run time, with test_offsets.xhtml failing first run and retry) is in no `fx-tests` output (known: killed jobs are left out).
- Workaround: Taskcluster API: CF task `dependencies[0]` is the action task, whose `payload.env.ACTION_TASK_ID` is the confirmed job.
- Suggestion: have `task` print the confirmed job for a `-cf` task, and flag it when it was killed at max run time.
