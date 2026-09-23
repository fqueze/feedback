## Marionette tests (telemetry-tests-client) have no data

- Question: "how often does this marionette test fail, per config, out of how many runs, and in which tasks?"
- Command: `fx-tests test toolkit/components/telemetry/tests/marionette/tests/client/test_fog_deletion_request_ping.py`
- Expected: rates per config and failing task ids, as for xpcshell/mochitest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2). `fx-tests intermittent --bug 2071124` worked, but only 10 of its 52 occurrences carry a failure message and it has no run denominators; `fx-tests task <id>` said "This profile records no tests at all" although the resource profile has `test` markers for every marionette test.
- Workaround: downloaded the 52 live_backing.log files and grepped them; run counts from the Treeherder jobs API (`/api/project/<repo>/jobs/?job_type_name=...`), history from `/api/failuresbybug/`.
