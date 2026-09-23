## How often does a WPT test fail, and in how many runs? (window-open-defaults.window.js)
- Command: `fx-tests test testing/web-platform/tests/html/browsers/the-window-object/window-open-defaults.window.js` (also `--history`)
- Expected: per-config pass/fail counts for a WPT test, or at least a clear "WPT is not covered" with a pointer to what is.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...", which reads like a typo in the path.
- Workaround: Treeherder API (push list, then jobs per push), task definitions (`MOZHARNESS_TEST_PATHS`) to find the chunk that ran the directory, then each job's `wptreport.json`. About 15 minutes of scripting for 12 m-c pushes.

## Which jobs ran this test and passed? (to compare a passing run)
- Needed a passing linux debug run of the same test. WPT chunk numbers change from push to push (wpt-1 ... wpt-29 for the same test within a week), so the job name cannot be guessed.
- No fx-tests command answers "the jobs that ran test X on push/day Y". Workaround as above.

## `fx-tests task <id>` lists expected ERRORs as failures
- Command: `fx-tests task JJ7HL97fTHSomBiVMe-YZw --profiles`
- Got: "FAILED (6) — every test this job recorded a failure for", including `/html/browsers/the-window-object/open-close/no_window_open_when_term_nesting_level_nonzero.window.html ERROR` with its subtest message. wptreport.json shows that ERROR is the expected status (no `expected` key). It appeared in all 11 jobs and looked like a leaker suspect until I checked.
- Expected: expected statuses marked as expected, or left out of FAILED.
