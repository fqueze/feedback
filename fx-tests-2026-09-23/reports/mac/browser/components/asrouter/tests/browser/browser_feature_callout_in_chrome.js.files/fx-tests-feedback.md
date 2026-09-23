# fx-tests feedback

## `try --all-jobs --test <path> --task-ids` prints no task IDs

- Question: which tasks are behind the "failed 2" row for this test on the try push?
- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/components/asrouter/tests/browser/browser_feature_callout_in_chrome.js --task-ids` (and the same with `--json`)
- Expected: the task ID of each job run counted in the per-configuration table (at least the failed ones), since `--task-ids` was passed.
- Got: only the counts per configuration (`debug-mochitest-browser-chrome-16  2 jobs, 0 passed, 0 passed on retry, 2 failed`); `--task-ids` silently ignored, and the JSON has no task IDs either.
- Workaround: queried the Treeherder jobs API for the push and filtered on the job name (`N3pqOFlVTA62r1nJ08nC8Q`, `ZiS9MNVaQCK5t9B5QFqFlw`), then `fx-tests task`.

## `task --messages` lists expected (`TEST-FAIL`) results with the failures

- Question: what failed in this job?
- Command: `fx-tests task N3pqOFlVTA62r1nJ08nC8Q --messages`
- Expected: only unexpected results, or expected ones marked as such.
- Got: `2x changed preference: browser.shell.mostRecentDefaultPromptSeen` (and two more prefs) listed with the leak messages as failures. In the log they are `TEST-FAIL` (a todo: `checkPreferencesAfterTest` records them with `todo: !gConfig.comparePrefs`, and CI does not pass `--compare-preferences`); only the leak messages of the retry are `TEST-UNEXPECTED-FAIL`. Likewise the first run's leak messages are `TEST-FAIL` (downgraded because the test was rerun), and nothing distinguishes them from the retry's unexpected ones.
- Workaround: downloaded `live_backing.log` and grepped `TEST-UNEXPECTED`.
