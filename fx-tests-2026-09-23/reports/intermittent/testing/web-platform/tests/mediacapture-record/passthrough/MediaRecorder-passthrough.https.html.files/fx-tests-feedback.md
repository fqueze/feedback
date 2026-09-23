## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/mediacapture-record/passthrough/MediaRecorder-passthrough.https.html`
- Expected: rates per config, failure modes, history, as for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — WPT is not covered, so no run counts, no per-config rate, no `--history`.
- Workaround: `fx-tests intermittent --bug 2047270 --since 110 --json` plus downloading the job logs; run counts stayed unknown.

## Question: "which failure mode did each annotated job of this bug have?"

- Command: `fx-tests intermittent --bug 2047270 --since 110` (and `--json`)
- Expected: occurrences grouped by failure mode (the failure lines of this test per job), so the timeout mode and the backlog UNEXPECTED-PASS/FAIL mode get separate counts, and jobs starred on the bug for other tests are set apart.
- Got: the "Failure messages" summary only counts jobs that have lines; 119 of 147 occurrences have an empty `lines` array (they are this test's subtest TIMEOUT, which is apparently not captured), so the summary showed only the minority backlog mode (14x) as if it were the main one.
- Workaround: a Python script over `occurrenceRows[].lines`, then downloading 7 logs to confirm the empty-lines rows are the timeout.
- What would have answered it: count and label the "no failure line captured" occurrences in the text output, and group occurrences by (test, first failure line) with platform/job counts.

## `intermittent --bug` cannot reach the unannotated failures of the same mode

- Not a bug, a gap: the aarch64 backlog-2 jobs failing the same way since Sep 21 (JDUFtT-2RHiaNOuWLTgr1w, PwVCWcKBSsSG6EhNTpEFoQ, XFRiAjzzQkef5XZ9qJQJig) were not annotated on this bug and had to be found via the Treeherder API by job name on each push.
