## `failures --message` does not find a crash signature that `crashes` lists

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind this message (it is the first-failure message of 80 runs of this test in `fx-tests test`'s Issues).
- Got: "No failure matched. Searched 4,982 tests in xpcshell-issues.json". `fx-tests crashes --harness xpcshell` lists it as the top signature (19,349 crashes, 503 tests), but has no `--tests`/`--signature` option to list those tests or their configs.
- Workaround: checked a few other tests from `fx-tests task <id>` one by one with `fx-tests test <path>` to see which configs they fail on.

## `test --bugs` prints nothing when no bug matches

- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_SelectableProfileBackupResource.js --bugs`
- Expected: a "Bugs: none found" line (or the bugs).
- Got: the usual header and Issues table, then nothing, so "no bug" and "flag ignored" look the same.
- Workaround: searched Bugzilla REST by summary.
