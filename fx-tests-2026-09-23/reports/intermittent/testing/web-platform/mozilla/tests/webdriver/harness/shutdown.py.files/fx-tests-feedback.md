# fx-tests feedback (shutdown.py report)

## Question: how often does this wdspec test fail per run, per config?

- Command: `fx-tests test testing/web-platform/mozilla/tests/webdriver/harness/shutdown.py`
- Expected: pass/fail counts per config for a web-platform-tests wdspec test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..., so this reports nothing about the test itself."
- Workaround: listed 1142 `android-em-14-x86_64/opt` wdspec jobs from the Treeherder jobs API, downloaded each job's `wpt_errorsummary.log`, and kept the jobs whose `test_groups` line holds `/_mozilla/webdriver/harness` (116 runs, 6 failures). About 10 minutes and 9 MB of downloads for one rate.
- What would have answered it: wpt (at least wdspec) in the `test` data, or a hint pointing at the errorsummary `test_groups` approach.

## `fx-tests task <id>` assumes run 0, and calls a missing artifact expired

- Command: `fx-tests task aBl1xkgMSaWuhlewfRa29Q --profiles`
- Expected: the profile of the run that failed (run 1; run 0 was `exception` / `worker-shutdown`).
- Got: "task aBl1xkgMSaWuhlewfRa29Q.0 has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent". The artifact was not expired: run 0 never produced it, and run 1 had it.
- Workaround: `fx-tests task aBl1xkgMSaWuhlewfRa29Q.1 --profiles`. (`fx-tests intermittent` had printed `.1`; I dropped the suffix.)
- Suggestion: when run 0 was an exception, say so and list the other runs, rather than blaming expiry.

## `fx-tests task` says a wdspec job's profile "records no tests at all"

- Command: `fx-tests task aBl1xkgMSaWuhlewfRa29Q.1 --profiles`
- Expected: the job's tests, including `/_mozilla/webdriver/harness/shutdown.py` failing.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one".
- But: `profiler-cli thread markers` on that same profile shows 116 `test` markers, including `OK — /_mozilla/webdriver/harness/shutdown.py` (1m30s), and a `FAIL` marker for the subtest. It looks like wpt's `test` markers are not recognised, so the message is wrong.
