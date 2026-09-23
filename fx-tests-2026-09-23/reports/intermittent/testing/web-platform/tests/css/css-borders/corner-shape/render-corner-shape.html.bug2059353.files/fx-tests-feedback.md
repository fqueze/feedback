# fx-tests feedback (render-corner-shape.html, bug 2059353)

## 1. `fx-tests task` reports an expected TIMEOUT as the job's failure and hides the real one (wpt test-verify job)

- Command: `fx-tests task HuOCEUuFTsWjq1kmVuOkSA --profiles`. The job is test-linux2404-64/debug-test-verify-wpt-1.
- Expected: the job's unexpected results. The log has two `TEST-UNEXPECTED-FAIL | /css/css-borders/corner-shape/render-corner-shape.html?...` lines, and those are why the job is orange.
- Got:
  - `FAILED (1)` with `/css/css-overflow/scroll-markers/chrome-555280390-crash.html TIMEOUT — 15 failing executions of 15`. In the log, all 15 of those are plain `TEST-TIMEOUT`, which is the expected status.
  - The render-corner-shape variants only show up in a warning, `222 failing markers in this job named no test path`. The ones it lists are expected `FAIL`s, not the two `UNEXPECTED-FAIL`s.
  - `Outcomes` does not mention an unexpected failure at all.
- Workaround: downloaded `live_backing.log` and grepped for `TEST-UNEXPECTED`.

## 2. `fx-tests test` has no wpt data

- Command: `fx-tests test testing/web-platform/tests/css/css-borders/corner-shape/render-corner-shape.html`.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`.
- Effect: I had no run counts, rates or history for a wpt test. The only numbers came from `fx-tests intermittent --bug`, which counts annotations. To get pass/fail per job, I listed each push's jobs through the Treeherder API: every test-verify-wpt job on the four pushes that touched the test.

## 3. The question "which failure mode is each annotated job?" needed the logs

- Command: `fx-tests intermittent --bug 2059353 --since 60 --full-messages --limit 0 --json`.
- The `lines` field is empty for 4 of the 19 occurrences: 3 test-coverage-wpt jobs and 1 web-platform-tests-reftest job. Those turned out to be different failure modes. The 3 coverage jobs show a TIMEOUT, then 43 ERRORs from `Failure while resetting counters`. The reftest job shows 5 UNEXPECTED-PASS.
- The text output's "Failure messages, per annotated job" block gives no hint that some jobs have no message. They are just missing from the counts.
- What would have answered it: a per-occurrence status (FAIL / PASS / TIMEOUT / ERROR) column, and "no failure line recorded" where there is none.

## 4. The question "where in the browser session did each failure happen?" needed a script

- There was nothing to run for this: `fx-tests` does not read wpt logs.
- Where it matters: for wpt, and test-verify in particular, the position of the unexpected result among the loads since the last `Application command` was the discriminating evidence. 9 of 25 failures were at exactly load 67.
- Workaround: `render-corner-shape.html.bug2059353.files/positions.py` and `perload.py` over the raw logs.
