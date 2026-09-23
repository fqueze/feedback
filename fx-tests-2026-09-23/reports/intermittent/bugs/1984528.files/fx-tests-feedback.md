# fx-tests feedback (bug 1984528)

## Question: which test was running when the crash behind this bug happened?

- Command: `fx-tests intermittent --bug 1984528 --limit 0`
- Expected: the tests the crashes happened in (the PROCESS-CRASH line names one: `PROCESS-CRASH | ... | browser/components/aiwindow/ui/test/browser/browser_smartwindow_switcher_telemetry.js`).
- Got: "Tests named, per annotated job" shows `132x ShutdownLeaks`, `18x leakcheck`, and the names of unrelated failures starred in the same jobs. Nothing names the test the crash happened in.
- Workaround: ran `fx-tests task <id>` on all 138 annotated tasks and grepped the `CRASH` rows. That took 138 calls.
- It could show: a "crashed in" count per test next to "Tests named".

## Question: how often does this test crash on windows11-32 debug?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_switcher_telemetry.js --task-ids --limit 0`
- Expected: roughly the 43 jobs of the last 7 days where `fx-tests task` reports `CRASH` for this test.
- Got: 10 crashes in 21 days, and `debug-mochitest-browser-chrome` at 1.5% (9/601). Only 3 of the 43 task IDs are listed.
- Likely reason: in most of these jobs the test logged TEST-PASS first, and the parent then crashed in the harness's between-test `Cu.forceCC()`. So the test's own status is PASS, and the crash is counted nowhere in the per-test index. `fx-tests task` does count it (`CRASH — 1 failing execution of 2`).
- Impact: the verdict ("intermittent, 1.5%") contradicts Treeherder, where the chunk running this manifest failed 44 of 45 times since 2026-09-18.
- It could show: a crash reported after test_end, attributed to the last test, or a warning that crashes after test_end are not counted.
