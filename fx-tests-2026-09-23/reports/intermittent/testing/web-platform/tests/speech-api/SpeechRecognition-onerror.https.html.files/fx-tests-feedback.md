# fx-tests feedback (SpeechRecognition-onerror.https.html, bug 2068853)

## `fx-tests test` does not cover web-platform-tests

- Command: `fx-tests test testing/web-platform/tests/speech-api/SpeechRecognition-onerror.https.html`
- Expected: rates per config and history for a WPT test (it is the bug's test, and `fx-tests intermittent --bug` finds it fine).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". No rates, no history, no task ids for any WPT.
- Workaround: `fx-tests intermittent --bug 2068853 --since 60 --tree all --history` for the annotated occurrences, then the Treeherder jobs API per push (`/api/jobs/?push_id=<id>&job_type_name=<exact name>`) to find when the job went green.

## `fx-tests task` counts an unexpected OK as a pass

- Question: "did this test fail in this job?" for a WPT whose failure is `TEST-UNEXPECTED-OK` (expected TIMEOUT) plus an unexpected subtest FAIL.
- Command: `fx-tests task IqubhIkPQxCTNUKDQgAQyg --limit 0 --full-messages` (and `--passed`).
- Expected: the test in the FAILED table, since the job is orange because of it.
- Got: absent from the 147 FAILED rows; with `--passed` it is listed as `OK 1`, among passes. The resource-usage profile's `test` marker does carry `Expected: TIMEOUT`, so the data to flag it as unexpected is there.
- Workaround: loaded the resource-usage profile in profiler-cli and read the `test` marker's `Expected` field.

## Finding which chunk ran a WPT on a push

- Question: "which job/chunk of config X ran test Y on push Z, and with what result?" (to confirm a test runs and passes after a metadata fix).
- No command answers it; I had to loop `fx-tests task <id> --passed` over every chunk of the config. `fx-tests test --coverage` would answer this if it covered WPT.
