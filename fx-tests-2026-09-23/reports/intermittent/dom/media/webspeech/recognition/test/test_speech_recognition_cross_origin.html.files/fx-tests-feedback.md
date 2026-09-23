## The failure mode shown is a TEST-KNOWN-FAIL line, not the unexpected failure

- Command: `fx-tests test dom/media/webspeech/recognition/test/test_speech_recognition_cross_origin.html` (also `fx-tests task <id> --profiles`, and the `Issues` block of every `test` variant)
- Expected: the issue is `A cross-origin track produces no transcript - got "mmhmm", expected ""`, the TEST-UNEXPECTED-FAIL of all 105 failing runs.
- Got: `105x FAIL The author of the test has indicated that flaky timeouts are expected. Reason: waiting for parakeet inference to complete`. That is SimpleTest.requestFlakyTimeout's TEST-KNOWN-FAIL (status FAIL, expected FAIL), which every run logs, passing or failing. It reads as if the test timed out.
- Workaround: `fx-tests task <id> --messages` lists both, and `fx-tests intermittent --bug` gets it right.
