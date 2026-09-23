## Question: pass/fail rate of a web-platform-test per config, over time

- Command: `fx-tests test testing/web-platform/tests/css/css-masking/mask-image/backdrop-filter-bad-mask-image.html` (also `--history`)
- Expected: per-config rates and a history for a wpt reftest, as for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only accepts xpcshell|mochitest. (`fx-tests task <wpt taskId>` and `fx-tests intermittent --bug` did work for wpt.)
- Workaround: listed every tsan and Android wpt-reftest job since 09-12 through the Treeherder jobs API (per `job_type_name`, 7 + 63 job types), then fetched ~5,500 `public/test_info/wpt_errorsummary.log` artifacts and parsed `test_groups` (was the test in the chunk) and `test_result` (status). About 10 minutes of scripting, and ~150 Treeherder/Taskcluster requests plus the artifact downloads.
- What would have answered it: `fx-tests test` over wpt data, where the errorsummary already has everything (group membership per chunk, status, known_intermittent). With the known_intermittent field, it could also show failures an annotation hides: here the test still fails 40/95 on tsan after being annotated `[PASS, FAIL]`.

## Question: which other test fails in the same jobs as this one (fallout)

- Wanted: "in the jobs where test A failed, what else failed, and does B fail only then?" Here, `backdrop-filter-mask-image-while-loading.html` (bug 2072589) fails in 40/40 of the post-annotation jobs where this test fails, and in 0 of the others.
- Workaround: a second pass over the same errorsummaries, cross-tabulating the two tests.
