## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_BackupService_regeneration.js --bugs`
- Expected: a line such as "Annotated bugs: none" so I know the lookup ran and found nothing.
- Got: the exact same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json` and a Bugzilla REST search on the summary.
