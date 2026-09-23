# fx-tests feedback (bug 2064985)

## Question: "what is the failure message of this test?"

- Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_glean.html` (also `--task-ids`, and `fx-tests task J_vrMMAmR6qOCVBiNOJKRw --profiles`)
- Expected: the Issues line / per-test message to be the failure, `Error executing test: TypeError: can't access property "values" of null` (the only TEST-UNEXPECTED-FAIL in the job).
- Got: `43x FAIL The author of the test has indicated that flaky timeouts are expected. Reason: WebRTC inherently depends on timeouts`. That message is a TEST-KNOWN-FAIL (Status FAIL, Expected FAIL) that SimpleTest.requestFlakyTimeout logs at the start of every WebRTC mochitest, so every failure of every WebRTC test would be reported under it.
- Workaround: read the Treeherder log lines from `fx-tests intermittent --bug <N> --json` (`occurrenceRows[].lines`), or the TEST-UNEXPECTED-FAIL marker in the profile.
- What could have shown it: skip statuses where status == expected (known fails) when picking the "first failure" message.

## Question: "on which pushes did these failing jobs run?"

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`)
- Expected: the revision per task, to tell failures before and after a fix landed.
- Got: task id, job name, day. The revision needs one `fx-tests task <id>` call per task (28 calls here).
- What could have shown it: a revision column (short hash) in the Task IDs list.
