## Question: does this web-platform-test variant fail per config, and does it depend on which variants ran before it?

- Command: `fx-tests test testing/web-platform/tests/fetch/api/crashtests/huge-fetch.any.js`
- Expected: per-config and per-variant (`.any.sharedworker.html`) pass/fail/skip counts, as for mochitest and xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` (wpt not covered).
- Workaround: `tree-order.py` and `order.py` in this directory: Treeherder push list per tree, then each job's `wpt_errorsummary.log` (to see whether the group ran) and `wptreport.json` (run order and status of the four variants, SKIP included). About 330 jobs over four trees, ~10 minutes.
- What would have answered it: wpt in the `test` command's data, with per-variant rows, SKIP counted apart from OK, and for each failing run the tests that ran before it in the same browser session (wpt restarts the browser per group and after a crash). The order dependency here (sharedworker crashes 29/29 when first in the browser on central, 0/33 after serviceworker/worker) was invisible without it.

## Question: which of the bug's annotations are this test, and which failure mode?

- Command: `fx-tests intermittent --bug 1903293 --since 90 --tree all`
- Got: 118 occurrences; "Tests named, per annotated job" lists only other tests' TEST-UNEXPECTED-FAIL lines (cookies, audiocontext), nothing for the CRASH/TIMEOUT of the bug's own test.
- Workaround: `summ.py` reading every occurrence's `wpt_errorsummary.log`.
- What would have answered it: count `TEST-UNEXPECTED-CRASH` and `-TIMEOUT` lines (and wpt `crash` actions) naming the bug's test, per occurrence.
