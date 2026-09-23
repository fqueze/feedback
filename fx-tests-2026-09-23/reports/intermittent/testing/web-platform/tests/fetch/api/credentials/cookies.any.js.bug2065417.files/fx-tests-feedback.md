## Question: which failure message did each annotated occurrence have (to find the one task with the rare `e=5; f=6; a=1` message)?

- Command: `fx-tests intermittent --bug 2065417 --since 30 --tree all --full-messages --limit 0`
- Expected: the Occurrences table to carry each occurrence's failure line (or a `--message <text>` filter), so the task behind one rare message can be found.
- Got: messages aggregated with counts, and a separate occurrences table with task ids but no message; the per-occurrence `lines` exist only in `--json` (`occurrenceRows[].lines`).
- Workaround: a Python script over `--json` joining `taskId` with `lines`.

## Question: rates and runs of a wpt test

- Command: `fx-tests test testing/web-platform/tests/fetch/api/credentials/cookies.any.js` (also `--history`)
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — no wpt data, so no run count or rate is available for this report; only sheriff annotations via `fx-tests intermittent`.
- Could have shown: a pointer that wpt is not covered and that `intermittent --bug/--test` is the available view. Note also that wpt `TEST-KNOWN-INTERMITTENT-FAIL` results (expected `[PASS, FAIL]` in metadata) are never annotated, so annotations undercount this failure.
