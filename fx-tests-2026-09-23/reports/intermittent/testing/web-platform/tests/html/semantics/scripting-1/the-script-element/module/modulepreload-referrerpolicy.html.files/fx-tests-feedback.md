# fx-tests feedback (modulepreload-referrerpolicy.html, bug 1979449)

## Question: how often does this WPT test fail per run, and on which configs?

- Command: `fx-tests test testing/web-platform/tests/html/semantics/scripting-1/the-script-element/module/modulepreload-referrerpolicy.html` (also with `--history`, and the `/html/...` WPT path form)
- Expected: runs and failures per config, like for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: `fx-tests intermittent --bug 1979449 --since 21 --limit 0` for annotated jobs only, then Treeherder `similar_jobs` for one config's pass/fail history. No per-run rate is available for WPT at all.

## Question: which subtest result made this WPT job orange?

- Command: `fx-tests task ZhhsyKxdQRSxxyIVS_hPtw --profiles`
- Expected: the test that the job was starred for (bug 1979449) among the failures.
- Got: "3 failing" listing three other tests (ERROR); modulepreload-referrerpolicy.html not listed, because its test status was OK and only a subtest was unexpected (`TEST-UNEXPECTED-PASS ... strict-origin ... - expected FAIL`). The resource-usage profile's TestStatus marker for that subtest also just says `PASS`, with no expected status, so nothing marks it as unexpected.
- Workaround: downloaded `public/test_info/wpt_errorsummary.log` for all 42 annotated tasks and scripted over it. The output could show WPT tests with any unexpected subtest result (PASS/FAIL vs expected) as failing, with the subtest name and message.

## Question: what failed in each annotated job of a bug?

- Command: `fx-tests intermittent --bug 1979449 --since 21 --limit 0 --full-messages`
- Expected: a failure message (or at least a status) for each of the 42 annotated jobs.
- Got: "Failure messages, per annotated job" sums to 25; the 17 jobs whose failure was an unexpected PASS (no assertion message) are silently absent, and "Tests named, per annotated job" also says 25x. Nothing says that 17 jobs had no message, which hid the second failure mode (the dominant one in the last 7 days).
- Workaround: same errorsummary script. The Occurrences table could carry the per-job subtest and status (e.g. `strict-origin: PASS, expected FAIL`).
