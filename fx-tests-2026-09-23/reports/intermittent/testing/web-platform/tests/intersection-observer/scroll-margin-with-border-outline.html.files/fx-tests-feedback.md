## Question: which jobs failed this WPT test, how often, on which configs

- Command: `fx-tests test testing/web-platform/tests/intersection-observer/scroll-margin-with-border-outline.html` (also `--history`)
- Expected: rates per config and history for a web-platform-test, or a message saying WPT is not covered.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". It reads as "never ran / renamed", not "this harness is not covered".
- Workaround: `fx-tests intermittent --bug 1970995 --since 30 --limit 0` for the annotated jobs, then each job's `wpt_errorsummary.log` by hand. No run count (denominator) is available anywhere.

## Question: what failed in this WPT job

- Command: `fx-tests task BV03HMr7SuGp8QSTyqJPAQ --profiles`
- Expected: the unexpected subtest FAIL of `/intersection-observer/scroll-margin-with-border-outline.html` (the job's annotated failure) listed under FAILED, plus the known-intermittent subtest FAILs of its sibling tests.
- Got: "1 failing": only `/css/css-ui/input-security-computed.html` (test-level ERROR). WPT subtest failures in a test whose own status is OK are not shown, although the resource-usage profile has a FAIL marker for them.
- Workaround: downloaded `public/test_info/wpt_errorsummary.log` for every annotated task and parsed it with a script.
