## Question: which tests are behind one crash signature (within a path)?
- Command: `fx-tests crashes --signature "child process hang at shutdown" --path browser/components/backup`
- Expected: the tests behind the row (like `failures --tests`), since the table has one row.
- Got: only counts (`218 crashes, 3 tests`); had to use `--json` to see which 3 tests.
- Workaround: `--json`. A `--tests` flag (automatic for <=3 rows, as in `failures`) would answer it.

## Question: is there a bug for this test?
- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_PreferencesBackupResource_Nimbus.js --bugs`
- Expected: a bugs section, or an explicit "no sheriff-annotated bug names this test".
- Got: the normal report with no bugs section and no line saying none were found, so "none" and "the lookup silently did nothing" look the same.
- Workaround: Bugzilla REST search by summary.

## Question: what kind of child process / stack is behind the 80 minidumps of one failure mode?
- Command: `fx-tests test <path> --task-ids --issue 1`, then `fx-tests crash <task> <dump> --frames 0` 80 times in a shell loop, grepping each for the executable and key frames.
- Expected: some grouping of the dumps of one issue by process type / top non-breakpad frame (the signature is a fixed override, so it says nothing about the stack).
- Got: one dump per invocation; for an overridden signature ("child process hang at shutdown") the Issues row hides that there are distinct stacks (here 63 in nsAppShell::Init, 17 in RenderThread::ShutDown, all in the GPU Helper).
- Workaround: shell loop over `fx-tests crash`, ~80 calls.

