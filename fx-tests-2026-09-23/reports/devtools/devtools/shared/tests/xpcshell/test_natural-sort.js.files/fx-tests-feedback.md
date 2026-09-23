## The bug the failing jobs were starred on
- Question: which bug did sheriffs star this test's failing jobs on?
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_natural-sort.js --bugs`
- Expected: bug 1991833, on which the three failing msix jobs were starred.
- Got: no bug, because the bug's summary names no test.
- Workaround: other reports in this run named it, then `fx-tests intermittent --bug 1991833`.
- Also needed: `fx-tests intermittent --bug 1991833` covers 7 days only, so to confirm the stars on the 08-31/09-03/09-07 jobs I queried Treeherder's `/api/jobs/?task_id=` and `/api/project/autoland/bug-job-map/?job_id=` by hand. `fx-tests test --task-ids` could print the bug each listed job was starred on.
