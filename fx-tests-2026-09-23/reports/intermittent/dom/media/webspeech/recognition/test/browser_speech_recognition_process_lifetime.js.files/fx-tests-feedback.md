## `errors --message` calls an unattributed message "specific to that test"

- Command: `fx-tests errors --message "hanging at shutdown; attempting crash report"`
- Expected: a message logged at browser shutdown (outside any test) reported as unattributed, not as test-specific.
- Got: `34 occurrences in 1 test — specific to that test, not ambient noise`, where the one "test" is the empty path `(not attributed to a test)`. The verdict line reads as the opposite of the truth.
- Workaround: read the per-test rows and ignore the verdict when the only path is empty.

## `test --bugs` prints nothing when no bug matches

- Command: `fx-tests test dom/media/webspeech/recognition/test/browser_speech_recognition_process_lifetime.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test.
- Got: the ordinary `test` output with no bugs section at all; only `--json` (`annotatedBugs: []`) showed the query ran and found nothing.
- Workaround: `--json` and read `annotatedBugs`.
