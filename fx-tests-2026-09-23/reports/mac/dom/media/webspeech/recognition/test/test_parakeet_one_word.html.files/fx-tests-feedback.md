## `fx-tests test` reports a TEST-KNOWN-FAIL (todo) as the failure message

- Command: `fx-tests test dom/media/webspeech/recognition/test/test_parakeet_one_word.html`
- Expected: under Issues, the assertion that failed: `The word is not delivered again over the silence that follows it, nor by the end-of-stream flush. Got 2: "dog |  yeah" - got 2, expected 1`.
- Got: `16x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: ...`. That is the `SimpleTest.todo(false, ...)` that `SimpleTest.requestFlakyTimeout` emits on every `setTimeout` call (TEST-KNOWN-FAIL in the log, see `testing/mochitest/tests/SimpleTest/SimpleTest.js`), present in every run, passing ones included. It is also what `--json` `messages[]` holds. It looks like a todo is taken as the first failure because its status is FAIL (expected FAIL).
- Workaround: `fx-tests task <taskId> --messages` on each failing task, which lists the real failure as the second message.
- Every test calling `requestFlakyTimeout` before its failure will be mis-summarized the same way (the sibling `test_speech_recognition_cross_origin.html` and `test_parakeet_endpoint_blank.html` too).

## `fx-tests test --bugs` prints nothing at all when no bug is found

- Command: `fx-tests test dom/media/webspeech/recognition/test/test_parakeet_one_word.html --bugs`
- Expected: a line such as "No sheriff-annotated bug names this test (2026-09-16..2026-09-22)".
- Got: the ordinary output with no bugs section; only `--json` (`annotatedBugs: []`) says the lookup ran and found none.
- Workaround: `--json`.
