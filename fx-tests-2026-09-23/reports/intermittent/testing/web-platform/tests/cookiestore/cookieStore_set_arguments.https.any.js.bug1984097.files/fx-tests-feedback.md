# fx-tests feedback (cookieStore_set_arguments.https.any.js, bug 1984097)

## `fx-tests test` has no web-platform-tests data
- Command: `fx-tests test testing/web-platform/tests/cookiestore/cookieStore_set_arguments.https.any.js`
- Expected: rates per config, history, and task IDs for a WPT test. The same tool's `intermittent --bug` already maps this bug to WPT jobs.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. That leaves no run count and no `--history`.
- Workaround: `fx-tests intermittent --bug 1984097 --limit 0` for task IDs, and Treeherder `api/failuresbybug/` for the longer history.

## `fx-tests task` hides WPT subtest failures (question: "did the starred test fail in this job?")
- Command: `fx-tests task Am3lXCMzR4-Ukz1KeYivZw --profiles` (and `--limit 0 --passed`)
- Expected: `/cookiestore/cookieStore_set_arguments.https.any.serviceworker.html` under FAILED, with its two `TEST-UNEXPECTED-FAIL` subtests. The sheriffs starred the job for exactly that.
- Got: the test is listed only under `--passed`, as `OK`. The resource-usage profile records test-level status, and a WPT subtest failure leaves the test `OK`. The FAILED list instead shows 24 `ERROR` tests, without saying whether each was expected, so it looks like the answer and is not.
- Workaround: download `public/test_info/wpt_errorsummary.log` and `wptreport.json`, and parse them by hand. `wpt_errorsummary.log` already holds each unexpected subtest result with `status` and `expected`, and could fill this list.
