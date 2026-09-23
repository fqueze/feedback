## Reading the test at the failing revision: hg.mozilla.org raw-file answers HTTP 406

- Command: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/65844140c333/browser/components/backup/tests/browser/browser_settings_restore_from_backup.js`
- Expected: the file at that revision, as the brief says.
- Got: empty body; `curl -I` shows 302 to hg-edge.mozilla.org, then HTTP 406 (also for mozilla-central/tip).
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg rev>` gives the git hash, then `git show <git hash>:<path>` in the local checkout (read-only).
- Suggestion: `fx-tests task` could print the git hash next to the hg revision it already shows.

## "Issues (first failure per run)" names a todo, not the failure

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_restore_from_backup.js`
- Expected: the message that made each run fail.
- Got: issue 2 (35x) is `handleEvent() was unable to perform a11y checks on hidden node: ...`, which is an `a11yWarn` -> `test_todo` (the per-test profile marks it `TEST-KNOWN-FAIL`, Expected: FAIL; the retry that passed still counts it under "Todo: 4"). The real failure, `Node is not accessible via accessibility API: id: main-button` (`a11yFail` -> `test_ok`, with a stack in the log), is present in 32 of the 34 jobs but listed as issue 4 with 1x. Likewise issue 3 (`changed preference: browser.backup.scheduled.last-backup-file`, 2x) is also a todo; those two runs really failed with `This test exceeded the timeout threshold`.
- Workaround: `fx-tests task <id> --messages` in a loop over all 34 task IDs, then reading one live log to tell todos from failures.
- Question it could have answered: "which non-todo message failed each run". In retry mode the first run logs todos and failures alike as `TEST-FAIL`; the known-fail/todo status (or the presence of a stack) is what separates them.
