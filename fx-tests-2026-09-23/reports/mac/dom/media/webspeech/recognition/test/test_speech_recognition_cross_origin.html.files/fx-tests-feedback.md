## `test` names a TEST-KNOWN-FAIL line as the failure

- Command: `fx-tests test dom/media/webspeech/recognition/test/test_speech_recognition_cross_origin.html` (and `--task-ids`, and `fx-tests task <id> --profiles`).
- Expected: the Issues line to be the TEST-UNEXPECTED-FAIL, `A cross-origin track produces no transcript - got "mmhmm", expected ""`.
- Got: `90x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: waiting for parakeet inference to complete` — the `SimpleTest.requestFlakyTimeout` todo, which is logged as TEST-KNOWN-FAIL (status FAIL, expected FAIL). It is listed first, and the real message is hidden behind `(+1 more message for this test; --messages to see them)`.
- Workaround: `fx-tests task <id> --messages`, or `fx-tests intermittent --bug <N>`, which did show the real message.
- Every mochitest that calls `requestFlakyTimeout` and then fails would be reported under this line, so a tree-wide `failures` view probably groups unrelated failures together under it.
