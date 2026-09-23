## wpt tests are not covered by `fx-tests test`
- Command: `fx-tests test testing/web-platform/tests/picture-in-picture/picture-in-picture-element.html`
- Expected: per-config rates for a wpt test (or, failing that, a message saying wpt is not in the data at all).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — reads as if the path were wrong.
- Workaround: Treeherder `similar_jobs` for the job, then `public/test_info/wptreport.json` of ~150 tasks, scripted. Cost: most of the investigation's data gathering.

## `fx-tests task` hides unexpected passes
- Question: "did the test this bug is about fail in this job, and how?"
- Command: `fx-tests task AsaNQJVJTbOk07QSQSYjoQ --limit 0`
- Expected: `/picture-in-picture/picture-in-picture-element.html` listed, since the job logged `TEST-UNEXPECTED-PASS` / `TEST-UNEXPECTED-OK ... expected TIMEOUT` for it and sheriffs starred that.
- Got: FAILED lists only TIMEOUT/ERROR outcomes; the unexpected OK is absent, so the failure the bug is about is invisible.
- Workaround: grep the job's live_backing.log.

## `fx-tests intermittent --bug` only sees 7 days
- Question: "which failure modes has this bug collected over its life?"
- Command: `fx-tests intermittent --bug 2043725`
- Got: 6 annotations, all backlog UNEXPECTED-PASS. The bug has 263 since May 29, 231 of them another mode (UNEXPECTED-TIMEOUT in regular wpt jobs, still live on mozilla-release).
- Workaround: `https://treeherder.mozilla.org/api/failuresbybug/?bug=N&startday=...&endday=...&tree=all`.
