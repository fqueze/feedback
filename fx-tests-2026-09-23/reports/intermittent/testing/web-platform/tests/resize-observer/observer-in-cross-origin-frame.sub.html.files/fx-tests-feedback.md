## `fx-tests test` does not cover web-platform-tests
- Command: `fx-tests test testing/web-platform/tests/resize-observer/observer-in-cross-origin-frame.sub.html` (also `--history`)
- Expected: rates per config, history, failing tasks for a WPT test.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only accepts xpcshell|mochitest.
- Workaround: `fx-tests intermittent --bug 2010741` for the annotated jobs, then Treeherder's jobs API to count how many ccov WPT jobs ran on mozilla-central (about 24 pushes), and the errorsummary `test_groups` line to find which chunk ran the test.

## `fx-tests task` hides WPT subtest failures
- Command: `fx-tests task DWxgoq4vRwC8tihbO-Gmiw --profiles`, which lists 14 failures in the job.
- Expected: the test the sheriffs starred on the bug (`/resize-observer/observer-in-cross-origin-frame.sub.html`, an unexpected subtest FAIL) among the failures.
- Got: it is missing from FAILED. It only shows up with `--passed`, as `OK`, because the harness status was OK and only a subtest failed unexpectedly.
- Workaround: grep the job's live_backing.log for TEST-UNEXPECTED-FAIL.
- Question it could answer: "which tests had unexpected subtest results in this job".
