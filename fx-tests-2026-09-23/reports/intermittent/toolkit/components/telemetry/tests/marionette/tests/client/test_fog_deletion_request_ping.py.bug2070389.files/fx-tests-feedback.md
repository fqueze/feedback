# fx-tests feedback (test_fog_deletion_request_ping.py, bug 2070389)

## "How often does this marionette test fail, and on which configs?"

- Command: `fx-tests test toolkit/components/telemetry/tests/marionette/tests/client/test_fog_deletion_request_ping.py`
- Expected: rates per config for a telemetry-tests-client (marionette) test.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. The message says it "may have been renamed, added after the window started, or never run in CI". None of those is true: the harness is simply not covered.
- Workaround: `fx-tests intermittent --bug` for annotated counts, and Treeherder similar-jobs (`treeherder-cli --similar-history <job-id>`) for runs per config. There are still no run counts across configs.
- Could say: "marionette/telemetry-tests-client is not in this data" instead of implying that the path is wrong.

## "Which failure message (and where) did each annotated job have?"

- Command: `fx-tests intermittent --bug 2071124 --since 30 --tree all --limit 0`
- Expected: the failure message per occurrence row. Two bugs here share the message `Error waiting for ping: Timed out` but differ by which wait timed out.
- Got: the default output aggregates messages ("9x ... 60.0 seconds") and lists occurrences without a message. `--json` `occurrenceRows[].lines` has them, but it is empty for most rows (81 of 100 here).
- Workaround: downloaded all 161 `live_backing.log`s and grepped for the `TEST-UNEXPECTED` line and the test file's traceback line number.
- Could show: the message per occurrence row, and flag rows whose message was not captured, so a bug's mis-starred jobs are visible.

## "What did this marionette job run?"

- Command: `fx-tests task H_NSJm3jQNq-HRF2sgXWYQ --profiles`
- Expected: the job's tests and outcomes.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". The harness actually ran about 20 tests. The telemetry-tests-client resource-usage profile has no test markers.
- Could say: that this suite does not record test markers, instead of suggesting the harness died.

## Default window of `intermittent --bug`

- Command: `fx-tests intermittent --bug 2070389`
- Got: `no sheriff annotations ... between 2026-09-16 and 2026-09-22`. The bug had 61 annotations up to 2026-09-14. The hint to widen with `--since`/`--tree all` worked; noting only that the 7-day default hid a bug fixed 8 days earlier.
