## Question: "what assertion actually failed in the 133 FAIL runs of this test?"

- Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_stats_oneway.html`
- Expected: the Issues block to show the failing assertion, e.g. `remote-outbound-rtp.remoteTimestamp is on the same timeline as remote-outbound-rtp.timestamp, and no older than 30 seconds. difference=-0.002s`.
- Got: `133x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: WebRTC inherently depends on timeouts` — that line is the SimpleTest.requestFlakyTimeout notice every run of this test emits, not a failure. `fx-tests task <id>` has the same line as the headline message; only `--messages` shows the real assertion.
- Workaround: run `fx-tests task <id> --messages` on each failing task and aggregate by hand to get the failure-mode breakdown. The Issues block could skip the flaky-timeout notice (or group by the other message), and could break FAIL down by message.

Addendum: that line comes from `SimpleTest.todo(false, ...)` in SimpleTest.js, i.e. a TEST-KNOWN-FAIL (status FAIL, expected FAIL). In the resource-usage profile of MK9Y1QdSTAyMOgtqVyYn4g it is a marker named `FAIL`, which is presumably why fx-tests counts it as a failure message. Filtering on expected != status would drop it.
