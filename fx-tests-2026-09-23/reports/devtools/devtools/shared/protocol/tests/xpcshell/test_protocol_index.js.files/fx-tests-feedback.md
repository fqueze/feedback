# fx-tests feedback (test_protocol_index.js)

## Question: "was this failure part of a job-wide collapse?"

- Command: `fx-tests test devtools/shared/protocol/tests/xpcshell/test_protocol_index.js` and `--task-ids`, `--bugs`.
- Expected: some sign that the one TIMEOUT came from a job where 1152 of 1552 tests "timed out" (task PyUxuOpdQj2b3T3XyQOKpg.0), which is a job-level failure (bug 1991833), not a failure of this test.
- Got: `1 timeout`, `Issues: TIMEOUT Test exceeded time limit`, and `--bugs` found nothing, since the job-level bug does not name the test. Only `fx-tests task` showed the scale (`1152 TIMEOUT`).
- Could have shown: next to a task ID, the fraction of the job's tests that failed ("1154/1552 failed in this job"), or a flag on runs from jobs where most tests failed, so a lone failure from a collapsed job is not read as the test's own.

## A launch that never happened is counted as a TIMEOUT

- In that job, the harness never created an xpcshell process for this test (`not killing -- proc or pid unknown`, `xpcshell return code: -1`); its TIMEOUT comes from the harness's kill timer firing 45 s after the launch failed, and was logged as expected (`expected=TIMEOUT`, retry mode). The retry never ran because the harness aborted on the next launch failure.
- fx-tests counts it as `1 timeout` in `fx-tests test` with no distinction from a real timeout. Not sure the data can tell them apart (the only clues are the ~20 ms between test start and `will retry`, and no test output), but it inflates per-test timeout rates on the msix config for every test in a collapsed chunk.
