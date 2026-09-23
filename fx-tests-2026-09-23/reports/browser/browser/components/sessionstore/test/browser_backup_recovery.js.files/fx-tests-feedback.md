## Question: is there a bug for this test, open or closed?
- Command: `fx-tests test browser/components/sessionstore/test/browser_backup_recovery.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: the normal `test` output with no bug section and no "none found" line, so it was unclear whether the flag did anything.
- Workaround: Bugzilla REST `bug?summary=browser_backup_recovery`, which finds bug 1435482 (same failure message, RESOLVED INCOMPLETE 2018) and four other closed intermittents.
