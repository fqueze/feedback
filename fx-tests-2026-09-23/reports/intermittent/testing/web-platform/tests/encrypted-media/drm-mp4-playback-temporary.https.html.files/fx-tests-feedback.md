## "How often does this WPT test fail, per config, and since when?" — no WPT data

- Command: `fx-tests test testing/web-platform/tests/encrypted-media/drm-mp4-playback-temporary.https.html` (and `--history`).
- Expected: per-config pass/fail counts, history and failing task IDs for a web-platform-tests test (bug 2068785 is a WPT intermittent).
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: Treeherder `jobs/?job_type_name=...&count=2000` for the three emewmf configs, then a script fetching ~1300 `public/test_info/wptreport.json` artifacts to count this test's status (and `known_intermittent`) per config and per day. About 15 tool calls.
- What would have answered it: WPT data in `fx-tests test`. The per-job `wptreport.json` has status, expected, known_intermittent and duration per test, and its order gives the neighbours.

## `fx-tests intermittent --bug 2068785`: "Tests named" empty for WPT timeouts

- Got: "(none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a test ...)". The annotated WPT failures are `TEST-UNEXPECTED-TIMEOUT` lines, which name the test.
- Expected: the test named from `TEST-UNEXPECTED-TIMEOUT` (and other `TEST-UNEXPECTED-*`) lines too.
