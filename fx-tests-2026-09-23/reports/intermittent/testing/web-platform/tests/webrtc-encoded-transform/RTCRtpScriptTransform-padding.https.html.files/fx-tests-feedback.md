## fx-tests: no WPT coverage in `test`

- Command: `fx-tests test testing/web-platform/tests/webrtc-encoded-transform/RTCRtpScriptTransform-padding.https.html` (also `--history`)
- Expected: per-config rates and history for a web-platform-test.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` for the job list, then downloaded ~460 `public/test_info/wptreport.json` artifacts and tallied the subtest status and duration with a script. The question was "how often does this WPT subtest get each status, per config": nothing in fx-tests answers it for WPT.

## fx-tests: TEST-UNEXPECTED-PASS on a subtest is invisible

- Command: `fx-tests task UTWCuR-ASG2riDAiMj5zWw --profiles`
- Expected: the job's starred failure (`TEST-UNEXPECTED-PASS | /webrtc-encoded-transform/RTCRtpScriptTransform-padding.https.html | ... - expected FAIL`) listed under FAILED.
- Got: "FAILED (3)" listing three other tests; the padding test, whose harness status is OK but whose subtest passed unexpectedly, is absent.
- Same for `fx-tests intermittent --bug 2070494`: "Tests named, per annotated job (none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a test)". An UNEXPECTED-PASS line names the test too.
- Workaround: grep of `live_backing.log`.
