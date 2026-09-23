## `intermittent --bug`: "what else failed in this job" is not answered, and it was the whole diagnosis

- Command: `fx-tests intermittent --bug 1723063 --since 30 --limit 0 --full-messages`
- Question: in each job annotated with the `test_marionette.py` message, what else failed earlier in the job?
- Expected: the job's other unexpected results, or at least a count per occurrence. `fx-tests task <taskId>` is the documented route, but it reports "0 tests" on marionette jobs (already logged in the sibling test_crash.py report's feedback).
- Got: each occurrence's `lines` holds only the line the sheriff classified. All 23 jobs also had `TEST-UNEXPECTED-ERROR | test_crash.py TestCrashInSetUp.test_crash_in_setup` 25 minutes earlier, and that error was the cause. Nothing in the output hinted at it.
- Workaround: downloaded 23 `live_backing.log` files (200 MB) and grepped them.
- What the output could have shown: the other `TEST-UNEXPECTED-*` lines of each annotated job, as the errorsummary has them.

## Mapping one failure message of a bug to its task IDs needs a script

- Command: `fx-tests intermittent --bug 1723063 --since 30 --limit 0 --full-messages` (text), then `--json` plus Python over `occurrenceRows[].lines`.
- Question: which task IDs have the "not less than 5" failure, and which have the test_crash.py one?
- Got: the text output lists the messages and the occurrences as two separate tables, with no join between them.
- What the output could have shown: the message (or the test) next to each occurrence row, or a `--message <substr>` filter on the occurrences.

## `test` has no marionette data

- Command: `fx-tests test testing/marionette/harness/marionette_harness/tests/unit/test_marionette.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains …". So there are no rates, configs or history for marionette tests. Job counts came from `treeherder-cli --similar-history`.
