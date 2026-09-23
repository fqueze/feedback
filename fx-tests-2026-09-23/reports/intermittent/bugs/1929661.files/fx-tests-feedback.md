## `fx-tests task` says a marionette-harness job ran no tests

- Command: `fx-tests task LWFtpX6pRnSpaOCt0GJ0RQ --profiles` (test-linux2404-64-shippable/opt-telemetry-tests-client)
- Expected: the 14 tests the job ran, all PASS, and the job failing after them (mozharness 1000 s no-output timeout after SUITE-END).
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". The job's resource-usage profile does hold 16 `test` markers (`PASS — telemetry/marionette/tests/...`) and a `suite` marker.
- Workaround: loaded the resource-usage profile with profiler-cli and listed `test` markers; read the log.

## Question: how often does a job-level failure (no test named) happen, per config?

- Commands: `fx-tests test toolkit/components/telemetry/tests/marionette/tests/unit/test_ping_server_received_ping.py` (no data: marionette suites are not in the xpcshell/mochitest data), `fx-tests intermittent --bug 1929661` (7 days of annotations only, no run counts).
- Needed: for a bug whose failures name no test (harness timeout after SUITE-END), failures and total runs per job name/platform over the window, and which suites the bug's annotations come from over a longer period.
- Workaround: Treeherder `jobs/<id>/similar_jobs/` for run counts, and `api/failuresbybug/?bug=1929661` (137 annotations since June, 14 suites) for the breakdown.
