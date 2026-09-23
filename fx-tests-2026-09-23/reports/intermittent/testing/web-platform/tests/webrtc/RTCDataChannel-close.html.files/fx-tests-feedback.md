# fx-tests feedback (RTCDataChannel-close.html, bug 2059721)

## `fx-tests test` has no web-platform-tests data
- Command: `fx-tests test testing/web-platform/tests/webrtc/RTCDataChannel-close.html`
- Expected: rates per config, failure modes, history for a WPT test (the bug is a WPT intermittent).
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` (and `--harness` only accepts xpcshell|mochitest).
- Workaround: Treeherder `api/failuresbybug/?bug=2059721` for the annotated failures and their messages, `api/failurecount/` for push counts. No run counts at all, so no failure rate.
- Question it could have answered: "how often does this WPT subtest fail, on which configs, since when".

## `fx-tests task` does not list WPT subtest failures
- Command: `fx-tests task INXpTBZNTb6wnkDgGdg0hA --profiles --messages`
- Expected: `/webrtc/RTCDataChannel-close.html` listed as failing (its subtest was TEST-UNEXPECTED-FAIL, the job is orange for it).
- Got: FAILED lists 3 other tests; RTCDataChannel-close.html absent, because its test-level status is OK (only a subtest failed). For ZEAiEI53RCGMDGqYtKn4ow the only row was an unrelated test.
- Workaround: read `public/test_info/wptreport.json` and filter subtests with `status != expected`.
- Question: "which subtests failed unexpectedly in this job".

## `fx-tests intermittent --bug` only covers 7 days
- Command: `fx-tests intermittent --bug 2059721`
- Got: 6 annotations 2026-09-16..22; the bug has 180 since 2026-07-30, with a different dominant message (Android `got 10`, 68 jobs) than the last week's (`got 1`).
- Workaround: Treeherder `api/failuresbybug/?startday=...`.
- Question: "which failure message dominates over the bug's life, per platform".
