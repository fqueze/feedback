## The "failure" message shown is a TEST-KNOWN-FAIL (todo), not the failure

- Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_glean.html` (and `--task-ids`, `--json`: `issues[]`, `messages[]`, `taskIds[].message`).
- Expected: the first unexpected message of each run: `Error executing test: TypeError: can't access property "values" of null` (checkDtlsKEA).
- Got: `43x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: WebRTC inherently depends on timeouts` as the only issue. In the profile that line is a `TEST-KNOWN-FAIL` marker (SimpleTest's todo for requestFlakyTimeout), logged several times before the real `TEST-UNEXPECTED-FAIL`. It seems a status=FAIL/expected=FAIL todo is taken as the first failure. Every WebRTC mochitest that calls requestFlakyTimeout will be grouped under this same misleading message.
- Workaround: `fx-tests task <taskId> --messages --full-messages` on one job shows the real message second.

## Question: "which pushes did this failure happen on?"

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`).
- The task list has day and job name but no revision/push, so telling "one bad push range, fixed by a landing" from "intermittent" needed one `fx-tests task <id>` call per task (28 calls) to read the revision line. A revision column (or a per-revision count) would have answered it directly: here all 43 failures were on three consecutive mozilla-central pushes.

## Verdict "intermittent" for a perma-fail over a push range

- Command: `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_glean.html`
- Got: `Verdict: intermittent. Fails on 23 configurations; worst ... 4.4%`, while all 43 failures are on one day and on three consecutive mozilla-central pushes; `fx-tests try 23480a99840e --project mozilla-central --all-jobs --test <path>` then showed every job of 38 configurations failing, 0 passing. The "Daily counts are not flat" hint is there, but a verdict like "failed every run on pushes X..Y, none since" would have saved the per-task revision lookups.

