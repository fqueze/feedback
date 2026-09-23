## `fx-tests test` files TEST-KNOWN-FAIL messages as the failure

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_turn_on_scheduled_backups.js` (and `fx-tests task VYJvaz8FQ3qEqLumSuEASA --profiles` / `--messages`)
- Expected: Issue 1 named after the TEST-UNEXPECTED-FAIL of those runs: `This test exceeded the timeout threshold ... Test ran for 48s, limit was 45s`.
- Got: `94x FAIL changed preference: browser.backup.scheduled.last-backup-file`, and `task --profiles` shows the same line as the failure. In CI `comparePrefs` is off, so browser-test.js logs every `changed preference:` result as TEST-KNOWN-FAIL (`pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`). They are logged just before the real failure, so "first failure message" picks them. `task --messages` lists all five messages with nothing to say which one is expected.
- Workaround: read the test's `--category Test` markers in the per-test profile, where the status is TEST-KNOWN-FAIL vs TEST-UNEXPECTED-FAIL.
- Cost: I spent a detour reading BackupService to find out why a real backup "sometimes" leaves prefs behind. It always does, and that is not a failure.

## Same thing for the a11y failure mode

- `fx-tests test …` Issue 3 is `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button`. In the per-test retry profile of GRGYFWmDR2atKezIPp4xZw one of them (in `test_enable_fails_without_backup_dir`) is logged as TEST-KNOWN-FAIL, while the resource-usage profile logs others as FAIL. So I could not tell from `fx-tests` which one is the unexpected failure. The message that also appears in those jobs, and that sheriffs annotated in bug 2002602, is `Node is not accessible via accessibility API: id: main-button, tagName: button`. The issue grouping would be more useful if it keyed on the first UNEXPECTED message.
