## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/webcodecs/full-cycle-test.https.any.js` (also the question behind `--history`)
- Expected: per-config rates and a per-day history for a WPT test, or a message saying WPT is not covered.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..., It may have been renamed, added after the window started, or never run in CI." That reads as "wrong path", not "harness not covered".
- Workaround: the Treeherder jobs API (`/api/project/autoland/jobs/?job_type_name=test-windows11-64-25h2/opt-web-platform-tests-webcodecs`) and per-job logs, with a script to bucket results by push. Question it had to answer: "is this WPT test still failing, and since which push" — a `--history` for WPT jobs would have shown it.

## `fx-tests task` on a WPT job: expected ERRORs listed as FAILED, the real unexpected ERROR only in a warning

- Command: `fx-tests task Z5vUciIHRfKpj4kGouSyLA --profiles`
- Expected: the job's unexpected results, i.e. `/webcodecs/full-cycle-test.https.any.worker.html?vp9_p2` ERROR (expected OK).
- Got: "FAILED (5)" listing media-source/* and image-decoder.https.any*.html, which the log shows as plain `TEST-ERROR` (expected). The one `TEST-UNEXPECTED-ERROR` is only in a stderr warning ("2 failing markers in this job named no test path"), together with the window variant that was `TEST-KNOWN-INTERMITTENT-ERROR`. The resource-usage profile's `test` marker has an `Expected` field (absent when status == expected) that could tell them apart.
- Workaround: grep `TEST-UNEXPECTED` in the live log.

## `fx-tests intermittent --bug` omits the bug's namesake failure line

- Command: `COLUMNS=250 fx-tests intermittent --bug 2073708 --limit 0 --full-messages`
- Expected: the harness-level line `full-cycle-test.https.any.worker.html?vp9_p2 | Error in remote: uncaught exception: Error: assert_equals: decoded pixel format ...` that the bug summary names, among "Failure messages" and "Tests named".
- Got: only video-encoder-hbd subtest messages and tests; full-cycle-test does not appear anywhere, though all 27 annotated logs contain its TEST-UNEXPECTED-ERROR.
- Workaround: downloaded all 27 logs and grepped.
