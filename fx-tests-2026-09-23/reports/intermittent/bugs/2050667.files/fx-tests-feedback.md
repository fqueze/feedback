## Which jobs ran the leaking wpt directory, and how many of them leaked (bug 2050667)

- Command: `fx-tests test testing/web-platform/mozilla/tests/webtransport/bfcache/failed.tentative.https.html` (and the `weak-promise.https.any.js` sibling)
- Expected: run/fail counts for the wpt directory whose browser session leaks (`leakcheck | ... for scope /_mozilla/webtransport/bfcache`).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — wpt is not covered, so a wpt-job leakcheck has no rate at all.
- Workaround: Treeherder `/api/jobs/?push_id=` + each task's `MOZHARNESS_TEST_PATHS` env to find the chunk that ran `/_mozilla/webtransport`, then grep its live log. A script, because nothing answers "which jobs ran this directory on this push, and did its leakcheck pass".

## `fx-tests task` on a leakcheck-only failure lists expected wpt ERRORs as the failures

- Command: `fx-tests task enwj9ZxfR96A07xMrKB3_A --profiles`
- Expected: the job's one unexpected result, `TEST-UNEXPECTED-FAIL | leakcheck | default 8092 bytes leaked (...)`, with its scope (`/_mozilla/webtransport`).
- Got: "91 failing" — every `ERROR` status (all expected per the wpt metadata, e.g. `/infrastructure/expected-fail/*`) listed as FAILED, plus a warning about 213 unattributed markers; the leakcheck failure is not shown.
- Workaround: grepped the live log.
