## Question: "when did this job start failing everything, and what was running then?"

- Context: every failing job of `devtools/server/tests/xpcshell/test_objectgrips-05.js` is a job where 470-1,560 tests timed out (`fx-tests task <id>`: "487 TIMEOUT, 41 PASS"), i.e. the test is one of hundreds of victims of a job-wide collapse.
- Command: `fx-tests test <path>` and `fx-tests task <taskId> --profiles`.
- Expected: `fx-tests test` to say that all 6 failing jobs were mass-failure jobs (e.g. "6 of 6 failing jobs had > 400 other failures"), and `fx-tests task` to point at the first failure / the moment tests stopped passing.
- Got: `fx-tests test` shows "TIMEOUT Test exceeded time limit" as if the test itself was slow; nothing flags that these jobs were collapses. `fx-tests task` lists 488 failures alphabetically, with no time order.
- Workaround: loaded each resource-usage profile and scripted over `profiler-cli --json` (first `Failed to launch` C++ warning, first `will retry`, the test's own markers).
- Could have shown: per failing job, the count of other failing tests (in `test --task-ids`), and in `task` the first failure in time order and the time after which nothing passed.

## Question: "which bug were these jobs starred with?"

- Command: `fx-tests intermittent --test devtools/server/tests/xpcshell/test_objectgrips-05.js --since 21`
- Got: "no sheriff-annotated bug names the test" — true, but all 6 failing jobs were starred with bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), found only through the Treeherder `jobs/?task_id=` and `bug-job-map/?job_id=` APIs.
- Could have shown: the bug each failing task was classified with, in `fx-tests test --task-ids` or `fx-tests task`.
