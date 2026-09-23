## `fx-tests test` "Issues (first failure per run)" names a TEST-KNOWN-FAIL line as the failure

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar_telemetry.js`
- Expected: the failure mode is the run's TEST-UNEXPECTED-FAIL, here `This test exceeded the timeout threshold ... Test ran for 91s, limit was 90s`.
- Got: `31x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`. In the profile that line is a `TEST-KNOWN-FAIL` (browser-test.js records changed prefs as todo unless `--compare-preferences` is on, which CI does not pass). It is present in every run of the test, passing or not, and only shows because it sorts before the real failure.
- `fx-tests task <id> --messages` has the same issue: KNOWN-FAIL and UNEXPECTED-FAIL lines are listed together with no status, so "changed preference" looks like the failure in every job.
- Workaround: loaded the per-test profile and read the `TEST-UNEXPECTED-FAIL` marker. Could have been shown by excluding (or tagging) known-fail/todo results in both views.
