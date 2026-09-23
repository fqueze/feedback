## `fx-tests test` "Issues (first failure per run)" ranks a TEST-KNOWN-FAIL as the first failure

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_create_backup.js`
- Expected: issue 2 "handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: button" (4x) to be a real first failure.
- Got: in the asan job em46HJ9JRj6PLKV5QktO6w that message is `Status: FAIL, Expected: FAIL` (TEST-KNOWN-FAIL, green) in the profile; the run's real failure is the later "uncaught rejection: Could not remove `/tmp/testBackup'…". `fx-tests task <id> --messages` for all 4 issue-2 jobs shows one of the two real failures next to it. So issue 2 is not a failure mode at all, and issues 1 and 3 are undercounted by 4.
- Workaround: `fx-tests task <id> --messages` per job, then check the status in the profile. The Issues list could skip messages whose expected status was FAIL, or at least mark them.
