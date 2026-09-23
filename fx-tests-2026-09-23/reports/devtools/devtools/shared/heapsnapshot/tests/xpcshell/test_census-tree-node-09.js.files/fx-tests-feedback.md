## Question: why did this Android xpcshell test FAIL, when no failure message was recorded?

- Command: `fx-tests task YbQIMH8bRhyaGF5iWwRDOw --messages --full-messages` (and `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census-tree-node-09.js`)
- Expected: some message for the FAIL row of `test_census-tree-node-09.js`, `dom/encoding/test/unit/test_misc.js` and `test_staticPartition_font.js`.
- Got: a bare `FAIL — 1 failing execution of 2` in `task`, and `Failure details not recorded (likely Android or platform logging issue)` in `test`'s Issues.
- The answer was in the same resource-usage profile, as an INFO marker in the test's time range: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. The three FAIL rows of that job all have it. When a FAIL has no TEST-UNEXPECTED message, the harness INFO line matching `Failed to start process: <test>` (or any INFO naming the test in its time range) would be a good fallback to show.
- Workaround: loaded the resource-usage profile with profiler-cli, `thread markers --search <test file name> --list`.
- Also: `fx-tests failures --harness xpcshell --message "Failure details not recorded"` finds nothing, so the placeholder shown under Issues cannot be searched to see how many other tests have the same unrecorded failure.
