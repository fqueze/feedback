## Issues rows name the profile-upload line instead of the failure

- Question: what did this test's 54 a11y-checks failures on 2026-09-08 actually fail with?
- Command: `fx-tests test toolkit/components/passwordmgr/test/browser/browser_preselect_login.js`
- Expected: an Issues row with the failure text, here `TEST-UNEXPECTED-PASS ... We expect at least one assertion to fail because this test file is marked as fail-if in the manifest.`
- Got: `24x FAIL profile uploaded in profile_browser_preselect_login.js.json`, `24x FAIL profile uploaded in profile_browser_preselect_login-2.js.json`, and six more `-3`..`-8` rows: 54 of 61 failures are labelled only by the profiler-upload log line, split into 8 rows by profile file name. `fx-tests task <id>` for one of those jobs also printed no message for the test.
- Workaround: loaded a profile and read the TEST-UNEXPECTED-* marker.
- Likely: the upload line is taken as the first failure message when the real one is TEST-UNEXPECTED-PASS (or it sorts first). Skipping "profile uploaded in" lines would fix it.

## `--bugs` printed nothing

- Command: `COLUMNS=300 fx-tests test <path> --bugs`
- Expected: a section listing sheriff-annotated bugs naming the test (bug 1840479 exists and names it).
- Got: the usual output with no Bugs section, no error, no "none found" line.
- Workaround: read the bug from Bugzilla directly.
