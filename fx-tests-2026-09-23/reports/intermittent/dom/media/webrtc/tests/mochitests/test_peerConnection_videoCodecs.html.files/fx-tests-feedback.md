## The headline failure message is a harness notice, not the failure

- Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_videoCodecs.html` (Issues row 5), and `fx-tests task at6UrfIJRJuAZC1LzPbloQ --profiles`
- Expected: the first real failing assertion, `media-source.framesPerSecond is a sane number for a short video test. value=172`.
- Got: `The author of the test has indicated that flaky timeouts are expected.  Reason: WebRTC inherently depends on timeouts` as the only Issues message for all 6 failures, and as the headline in `task`, with the real one hidden behind "(+1 more message ... --messages)". That notice is logged by `SimpleTest.requestFlakyTimeout` in every WebRTC test that calls it (it appears in passing neighbours too, e.g. test_peerConnection_addAudioTrackToExistingVideoStream.html in the same job), so it groups every failure mode of every WebRTC test under one meaningless row.
- Workaround: `fx-tests task <id> --messages --full-messages` on each task.

## Finding passing runs' task IDs

- Question: "a passing job of this test on the same config, to compare the same step succeeding" (the resource-usage profile of any job holds the test's full log on Android).
- Command: `fx-tests test <path> --task-ids` lists only failing tasks; `--coverage`/`--durations` give counts and durations but no task IDs.
- Could show: a few passing task IDs per config (`--task-ids --passing`).
