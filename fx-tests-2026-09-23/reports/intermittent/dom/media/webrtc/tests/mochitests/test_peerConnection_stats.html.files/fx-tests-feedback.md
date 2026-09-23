## Issues block keys on an INFO line every run prints, hiding the real failure modes

Question: "which failure modes does this test have, and how many of each?"

Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_stats.html` (also `--json`, `.issues` and `.messages`)

Expected: the 291 FAILs split by their actual failure message (here mostly
`remote-outbound-rtp.remoteTimestamp is on the same timeline as remote-outbound-rtp.timestamp ... difference=-0.002...s`,
plus others).

Got: a single row, `291x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: WebRTC inherently depends on timeouts`.
That is SimpleTest's info line printed by every test that calls `requestFlakyTimeout`, not a failure. `fx-tests task <id> --messages`
shows it as the first of the test's messages, with the real `TEST-UNEXPECTED-FAIL` after it. The `.messages` JSON array has the same one entry,
so no amount of scripting over `test --json` recovers the split.

Workaround: loop `fx-tests task <taskId> --messages` over a sample of the task IDs and count by hand.

## `intermittent --bug` ranks a harness summary line as the top failure message

Question: "what did the jobs sheriffs starred on this bug actually fail on?"

Command: `fx-tests intermittent --bug 1780717`
Expected: per annotated job, its TEST-UNEXPECTED-* / CRASH line (here 8x the remoteTimestamp message, 2x NS_ABORT_OOM, 1x xul.dll crash).
Got: `8x Finished in <n>ms` as the top "Failure message", then 9 remoteTimestamp lines each counted once because the `difference=` value differs; the 3 crashes do not appear at all.
Workaround: `fx-tests task <id> --messages` for each of the 11 occurrence task IDs. Normalising numbers in messages (as `<n>ms` already is) would also merge the 9 remoteTimestamp rows.
