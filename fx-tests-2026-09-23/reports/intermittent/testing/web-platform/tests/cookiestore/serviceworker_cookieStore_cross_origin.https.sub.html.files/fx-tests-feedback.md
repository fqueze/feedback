# fx-tests feedback (serviceworker_cookieStore_cross_origin.https.sub.html)

## No WPT data at all: "how often does this WPT subtest fail, per config?"

- Command: `fx-tests test testing/web-platform/tests/cookiestore/serviceworker_cookieStore_cross_origin.https.sub.html`
  (and `--harness wpt`, which is rejected: "--harness expects xpcshell or mochitest").
- Expected: rates per config, or at least a pointer to where WPT data can be had.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". Nothing about runs or passes.
- Workaround: Treeherder `api/jobs/?push_id=` for every autoland and mozilla-central push over a week,
  kept `test-windows11-{32,64}-25h2/debug-web-platform-tests-N`, then read the first line of each job's
  `public/test_info/wpt_errorsummary.log` (the `groups` list) to keep the 184 jobs that ran `/cookiestore`,
  then parsed their `test_result` lines. About 4,400 HTTP requests and three scripts
  (`find_jobs2.py`, `check_jobs.py`, `check2.py` in this directory).
  What could have shown it: `fx-tests test <wpt path>` giving runs and unexpected subtest failures per config.

## `fx-tests task` hides the failure the job was starred for

- Command: `fx-tests task VRAFef0aSDeA3OGy4SBp1w --profiles`
- Expected: the WPT test with `TEST-UNEXPECTED-FAIL` on a subtest
  (`/cookiestore/serviceworker_cookieStore_cross_origin.https.sub.html`) listed under FAILED,
  since that is why the job is orange and why a sheriff starred it.
- Got: "FAILED (7) — every test this job recorded a failure for" listing seven tests with ERROR status, and not this one:
  its test status is OK, only a subtest failed. The heading says "every test", so the omission reads as "did not fail".
- Workaround: grep `live_backing.log` and `wpt_errorsummary.log`.

## "What else failed in the same browser session before this WPT test?"

- Wanted: for one job, the tests that ran in the same browser before the failing one, with their unexpected
  and known-intermittent subtest failures (the leaker search the brief asks for).
- Command used: a Python script over `live_backing.log`, cutting from the last `Starting runner` before the
  test's `TEST-START`.
- What could have shown it: `fx-tests task <id>` listing, for the failing test, the tests before it in the
  same browser session and their failing subtests, known-intermittent ones included.
