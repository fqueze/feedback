# fx-tests feedback

## `fx-tests test` has no reftest data
- Command: `fx-tests test layout/reftests/svg/text-layout-10.svg`
- Expected: rates per config for a reftest with 124 sheriff annotations in 7 days (bug 2067348).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — `--harness` only accepts xpcshell|mochitest.
- Workaround: `fx-tests intermittent --bug 2067348 --limit 0` for the annotated jobs, and the job logs for everything else. No run counts, so no failure rate.

## `fx-tests task` does not attribute a reftest failure to its test
- Command: `fx-tests task REkZspRDSg2FiHUBzO_9ZA --profiles`
- Expected: the failing reftest listed as a failure.
- Got: "warning: 1 failing marker in this job named no test path ... FAIL layout/reftests/svg/text-layout-10.svg == layout/reftests/svg/pass.svg" and "89 tests, 89 executions, 0 failing ... No test-level failure in this job." The marker names the test; it is the reftest `A == B` form that is not parsed as a path.
- Workaround: read the job log.

## Question: the failure rate of a reftest on each config
- Command: `fx-tests test layout/reftests/svg/text-layout-10.svg` (and `--harness` accepts only xpcshell|mochitest)
- Expected: runs and failures per config, as for mochitests, to tell a 92% macOS nogpu rate from a 50% one.
- Got: no reftest data at all; `fx-tests intermittent --bug` counts annotated jobs, which has no denominator.
- Workaround: Treeherder `similar_jobs` for one job of each config, then each job's `public/test_info/reftest_errorsummary.log` for the `group_result` of `layout/reftests/svg/reftest.list` (script `rate.py` in this directory). About 1,500 artifact downloads.
