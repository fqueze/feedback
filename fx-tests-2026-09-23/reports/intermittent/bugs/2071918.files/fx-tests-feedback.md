# fx-tests feedback (2071918)

## `fx-tests task` says the resource-usage profile of a gtest job "records no tests at all", but it does
- Command: `fx-tests task Ti4QB-lnQvS5EW8FBC1RyQ --profiles` (test-windows10-64-2009-qr/opt-gtest-1proc)
- Expected: the job's gtest failures, with the test they belong to (e.g. `ThreadShutdownAnnotationsTest.CollectionSeesOnlyShuttingThreads` FAIL at t=7m26s).
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". A warning above lists 14 failing markers "named no test path". Loading the same profile in profiler-cli shows 15,541 `test` markers, one per gtest (`FAIL — ThreadShutdownAnnotationsTest.CollectionSeesOnlyShuttingThreads`), plus a FAIL TestStatus marker with the file:line.
- Workaround: loaded the resource-usage profile in profiler-cli and searched the markers by name.

## Question: "how often does this gtest fail, per config, and out of how many runs?"
- Command: `fx-tests test xpcom/tests/gtest/TestThreadShutdownAnnotations.cpp` -> "No test path in the xpcshell and mochitest 21-day data contains ...". There is no gtest harness.
- The question: the failure rate of a gtest per config, and whether it is steady since it landed.
- Workaround: a script over Treeherder's `similar_jobs` and `text_log_errors` for each gtest-1proc job type (`2071918.files/rate.py`, 11 job types, about 5,000 jobs). It could be shown as `fx-tests test` already shows xpcshell/mochitest: gtests have per-test markers in the resource-usage profiles.
