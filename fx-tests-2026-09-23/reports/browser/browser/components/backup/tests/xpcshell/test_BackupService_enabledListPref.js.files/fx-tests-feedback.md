## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_BackupService_enabledListPref.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`; no line about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"`, which returned `{"bugs":[]}`.
