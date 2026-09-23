## Question: "how often does this crashtest fail, per config, and since when?"

- Command: `fx-tests test gfx/tests/crashtests/1765667.html` (also `--history`)
- Expected: rates per config, like for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains "gfx/tests/crashtests/1765667.html"`. `--harness` only accepts xpcshell|mochitest, so reftest/crashtest paths have no rate, no history, no task list.
- Workaround: `fx-tests intermittent --bug 2072449 --limit 0` for the annotated failures, then the Treeherder jobs API (`/api/project/<repo>/jobs/?job_type_name=...`) and a script to count success/testfailed runs per config and per day.
- What would have answered it: reftest/crashtest support in `fx-tests test`, or at least the run count per job type next to the annotation counts in `fx-tests intermittent --bug`.

## Question: "did this test fail once or twice in this job?"

- Command: `fx-tests task K1dSLrp-RSi2KzQM_gf3oQ --profiles`
- Expected: one failing execution (the test ran once; the job then had two minidumps: the content process that hit the assertion, and the parent killed by the harness after the 370 s timeout).
- Got: `CRASH — 2 failing executions of 2`, with both signatures listed as if they were two runs of the test.
- Workaround: read the log: one TEST-START, one TEST-UNEXPECTED-FAIL, two PROCESS-CRASH lines.
- What would have shown it: one execution with two crash signatures, and each dump's process type (main / content).
