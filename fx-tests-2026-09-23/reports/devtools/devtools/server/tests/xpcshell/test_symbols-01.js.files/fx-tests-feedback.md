## Question: what did this job record for my test
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (default), then `COLUMNS=250 fx-tests task <id> --profiles --limit 0 > file` and grep.
- Expected: a way to see the row of one test in a 488-failure job (e.g. `--test <path>`), or the default output placing it when the job was reached from `fx-tests test <path> --task-ids`.
- Got: the first ~20 failing tests alphabetically (browser/components/backup/...), `… 468 more`. The 1969-line full list had to be dumped and grepped, six times.

## `fx-tests test <path> --bugs` prints no bug section when there is none
- Command: `fx-tests test devtools/server/tests/xpcshell/test_symbols-01.js --bugs`
- Expected: a "Bugs: none name this test" line.
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look identical.
