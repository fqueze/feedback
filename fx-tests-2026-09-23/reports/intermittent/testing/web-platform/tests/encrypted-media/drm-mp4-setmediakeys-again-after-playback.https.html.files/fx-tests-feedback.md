## "How often does this WPT test fail, per config?" — no WPT support

- Command: `fx-tests test testing/web-platform/tests/encrypted-media/drm-mp4-setmediakeys-again-after-playback.https.html` and the same with `--harness wpt`.
- Expected: per-config pass/fail counts and failing task IDs for a web-platform-tests test (bug 2068786 is a WPT intermittent, 20 annotated jobs).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..."; `--harness wpt` -> "--harness expects xpcshell or mochitest".
- Workaround: Treeherder `failuresbybug` + `jobs/<id>/similar_jobs` for the job list, then a script fetching each task's `public/test_info/wptreport.json` to count this test's status per config (and its co-occurrence with another test's TIMEOUT in the same job). About 10 tool calls and ~400 artifact downloads.
- What would have answered it: WPT data in `fx-tests test` (the wptreport.json per job has per-test status and duration), and a "which other tests failed in the same jobs" view.
