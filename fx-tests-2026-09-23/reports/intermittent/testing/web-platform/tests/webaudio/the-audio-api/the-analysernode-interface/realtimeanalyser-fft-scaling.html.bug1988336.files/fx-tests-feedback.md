## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/webaudio/the-audio-api/the-analysernode-interface/realtimeanalyser-fft-scaling.html`
- Expected: rates per config, history, and failing task IDs for a wpt test (the brief's step 1).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only accepts xpcshell or mochitest, so there is no rate, no `--history`, and no `--task-ids` for any wpt test.
- Workaround: `fx-tests intermittent --bug 1988336 --limit 0` for the task IDs, plus Treeherder's `/api/failuresbybug/` for a window longer than 7 days, plus reading the raw job logs. Checking that the test passed after the metadata fix took hand-written Treeherder `/api/jobs/?push_id=..&job_type_name=..` queries per chunk and grepping each log.

## `fx-tests task` does not list TEST-UNEXPECTED-PASS

- Command: `fx-tests task TCYl46zbQ9OOEVgcCpbx3Q --profiles`
- Question: "why did the sheriffs star this job on bug 1988336?"
- Expected: the test the job was starred for, with its `TEST-UNEXPECTED-PASS | ... | expected FAIL` line.
- Got: 96 "failing" tests, all of them expected ERRORs (speculation-rules, fedcm, ...), and no mention of realtimeanalyser-fft-scaling.html. The job's resource-usage profile also records the subtest as a plain green `PASS` TestStatus marker, with nothing saying it was expected to FAIL.
- Workaround: downloaded `live_backing.log` and grepped it.
- What could have answered it: list unexpected results (including unexpected passes) against the wpt expectations, and leave out ERRORs the metadata expects.
