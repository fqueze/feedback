# fx-tests feedback (httponly_cookies.https.window.js)

## `fx-tests test` does not cover web-platform-tests

- Command: `fx-tests test testing/web-platform/tests/cookiestore/httponly_cookies.https.window.js`
- Expected: rates per config and failure modes for a WPT test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only accepts xpcshell|mochitest.
- Workaround: `fx-tests intermittent --bug 1983795 --tree all --since 30` for annotations, then Treeherder API + `wpt_errorsummary.log` (its `groups` line) to find the jobs that ran `/cookiestore`, then grep each job's `live_backing.log`. That scan took ~20 minutes of tool time for 21 jobs. There was no way to get a pass/fail rate for the test.

## `fx-tests task` hides WPT subtest failures

- Command: `fx-tests task Ufg7sMsdQRCgwFK0j4QIqA --profiles`
- Expected: `/cookiestore/httponly_cookies.https.window.html` listed as failing (the job was starred for its `TEST-UNEXPECTED-FAIL` subtest).
- Got: the test is not in FAILED at all. It shows up only under `passed` in `--json`, with status `OK`, because WPT reports the harness status (`OK`) apart from the subtest results (`FAIL`).
- Workaround: grep the job log.

## Question: which failure message did each annotated occurrence have?

- Command: `fx-tests intermittent --bug 1983795 --tree all --since 30 --json | python3 ...` over `occurrenceRows[].lines`.
- Why: the text output lists message counts and occurrences, but not which message belongs to which occurrence. So you cannot see that the 5 non-Windows jobs are all one push with a different leftover cookie (`uppercase=cookie-value`), unlike the 28 Windows ones (`cookie-name=deleted`).
- What would have answered it: a message-tag column in the Occurrences table, or `--group-by message`.
