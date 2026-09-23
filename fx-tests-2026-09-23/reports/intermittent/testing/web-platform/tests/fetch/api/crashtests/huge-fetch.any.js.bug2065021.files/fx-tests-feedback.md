## Question: how often does a web-platform-test fail, per config, and since when?

- Command: `fx-tests test testing/web-platform/tests/fetch/api/crashtests/huge-fetch.any.js` (also tried `--history` mentally; same data)
- Expected: per-config pass/fail rates for the wpt variants (`.worker.html`, `.sharedworker.html`, ...), as for mochitest/xpcshell.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` — wpt is not covered at all.
- Workaround: a script (`rate.py` in this directory) listing mozilla-central pushes from the Treeherder API, picking `test-windows11-32-25h2*/…-web-platform-tests-N` jobs, and reading each job's `public/test_info/wpt_errorsummary.log` (the `groups` line says whether the test's directory ran there, `crash` lines give the crash per variant). About 150 jobs, several minutes, and it cannot see TEST-SKIP (tests skipped by `--skip-crash` look like passes unless you read the full log).
- What would have answered it: wpt in the `test` command's data, with per-variant (`.any.worker.html`) rows and SKIP counted separately from OK.

## Question: which of the bug's annotations are really this test?

- Command: `fx-tests intermittent --bug 2065021 --since 30 --tree all`
- Got: 134 annotations, 28 of them `test-update-integrity-*` jobs with `TEST-UNEXPECTED-FAIL: differences found after update` that sheriffs starred on this bug by mistake. The "Tests named, per annotated job" block listed only 2 unrelated cookie tests, not huge-fetch.
- Workaround: read the occurrence list by job name.
- What would have answered it: a per-occurrence "names this bug's test: yes/no" column, or a count of annotations whose failure line names the bug summary's test.
