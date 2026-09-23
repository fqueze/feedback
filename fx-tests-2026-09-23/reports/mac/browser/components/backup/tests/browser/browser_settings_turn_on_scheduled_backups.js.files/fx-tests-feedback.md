## Issues names a TEST-KNOWN-FAIL message as the failure

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js`
- Expected: the issue for the 85 Linux a11y-checks failures named by the unexpected result, `This test exceeded the timeout threshold ... limit was 45s`.
- Got: `2. 85x FAIL changed preference: browser.backup.scheduled.last-backup-file`. In the profile of N8WOkP63RUa4uJ8MOuClcw.0 the four `changed preference:` results are `TEST-KNOWN-FAIL` (browser-test.js records them with `todo: !gConfig.comparePrefs`); the only `TEST-UNEXPECTED-FAIL` is the timeout-threshold one, which comes after them. `fx-tests task <id> --messages` also lists all five with no expected/unexpected distinction.
- Workaround: read the per-test profile's `TEST-UNEXPECTED-FAIL` marker, or `fx-tests task --messages` on several jobs, to find the message that actually failed.
- Cost: I started from the wrong failure mode (a pref leak) until the profile showed the prefs were known-fail.

