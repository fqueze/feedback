## Issues list names a TEST-KNOWN-FAIL as the failure mode

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js`
- Expected: issue 1 ("first failure per run", 94x) to be the TEST-UNEXPECTED-FAIL that made the run fail: `This test exceeded the timeout threshold ... Test ran for Xs, limit was 45s`.
- Got: `FAIL changed preference: browser.backup.scheduled.last-backup-file`. In the profile these `changed preference:` results are TEST-KNOWN-FAIL (todo, `pass: !gConfig.comparePrefs` in browser-test.js), logged just before the real failure, so "first" picks a non-failure. `fx-tests task <id> --messages` lists both without saying which is known-fail.
- Workaround: loaded the per-test profile and read the TEST-* markers to find the actual TEST-UNEXPECTED-FAIL. Cost: one profile load and a misleading first hypothesis (pref leak).
- Suggest: drop TEST-KNOWN-FAIL / TEST-EXPECTED-FAIL results from the issue ranking, or at least tag them.
