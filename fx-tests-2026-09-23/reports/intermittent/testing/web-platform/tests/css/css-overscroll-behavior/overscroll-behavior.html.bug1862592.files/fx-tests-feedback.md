## Question: how often does this WPT test fail, per config, before and after a metadata change?

- Command: `fx-tests test testing/web-platform/tests/css/css-overscroll-behavior/overscroll-behavior.html`
- Expected: per-config rates and a history, as for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". WPT is not covered at all.
- Workaround: Treeherder `jobs/<id>/similar_jobs/` for both backlog configs, then downloaded ~250
  `wpt_errorsummary.log` artifacts and scripted over them, splitting by the push that changed the
  test's metadata. What would have answered it: `fx-tests test` for WPT paths, with subtest-level
  unexpected results (status vs expected, known_intermittent) per config and per day.

## Question: what failed unexpectedly in this WPT job?

- Command: `fx-tests task dbVnCZCDQguEBLLviZ1BRg --profiles`
- Expected: the unexpected results (the job's `wpt_errorsummary.log` has 2 tests plus 2 crashes).
- Got: "347 failing", listing every test whose status was not OK/PASS, including tests whose
  TIMEOUT/ERROR was the expected status (this is a backlog job: it only runs tests expected to
  TIMEOUT). The one unexpected subtest result (TIMEOUT, expected PASS) was not shown.
- Workaround: read `wpt_errorsummary.log` and `wptreport.json` directly.
