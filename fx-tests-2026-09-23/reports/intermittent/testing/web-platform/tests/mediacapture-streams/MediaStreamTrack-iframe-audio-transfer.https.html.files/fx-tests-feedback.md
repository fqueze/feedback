# fx-tests feedback (MediaStreamTrack-iframe-audio-transfer.https.html, bug 2034727)

## 1. `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/mediacapture-streams/MediaStreamTrack-iframe-audio-transfer.https.html`
- Expected: rates per config, failure modes, history for a WPT test (the brief starts every diagnosis with it).
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". Same for `--history`, `--task-ids`.
- Workaround: Treeherder `api/failuresbybug/?startday=..&endday=..&tree=all&bug=2034727` (634 annotated jobs), then `api/project/<tree>/jobs/<job_id>/text_log_errors/` for the 289 jobs with no stored lines, and a script to sort them by failure mode and by `machine_name`.
- The question it could not answer: "which failure modes, on which configs, since when" for a WPT test. It also has no run counts, so the failure rate stays unknown.

## 2. `fx-tests intermittent --bug` counts trunk only, and misses most of the bug

- Command: `fx-tests intermittent --bug 2034727`
- Got: "3 sheriff annotations on trunk, 2026-09-16 to 2026-09-22". Treeherder has 30 more in the same window, on mozilla-beta and mozilla-esr153, and 634 since 2026-07-20.
- The question: "is this still failing anywhere after the fix landed?" The only jobs still failing are on the release branches, which this view leaves out. A line such as "N more on non-trunk trees" would have answered it.

## 3. `fx-tests task` on a WPT job lists the wrong tests as failing

- Command: `fx-tests task JFYTV2S4SYyu-E9z6DB4sg --profiles`
- Expected: this test listed. Its subtest was `TEST-UNEXPECTED-FAIL` (NotAllowedError, expected TIMEOUT), and sheriffs starred the job on this bug.
- Got: the test was not listed at all. Its harness status was `TEST-KNOWN-INTERMITTENT-OK`, so a subtest's unexpected status seems to be dropped.
- The reverse also happens. On `DGuGwlkVTJCkH0R1WeWCxg` and `TbgAC15NTouEjh0gHHQKmg` the test is listed as "ERROR — 1 failing execution", with no message. Its harness ERROR was expected (`TEST-KNOWN-INTERMITTENT-ERROR`). The unexpected part, subtest `TEST-UNEXPECTED-TIMEOUT` (expected FAIL), is not shown.
- Workaround: grep the job's `live_backing.log` for `TEST-UNEXPECTED`.
