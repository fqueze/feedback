## Question: "the tests behind this crash signature"

- Command: `fx-tests crashes --signature 'storage::Service::Observe' --limit 0`
- Expected: the list of tests hitting `@ mozilla::storage::Service::Observe`, with counts (to see whether the crash is specific to one test or shared).
- Got: one row `327 crashes, 28 tests` and no test names. `fx-tests failures --harness xpcshell --message 'storage::Service::Observe' --tests` found nothing (crash signatures are not messages), and `fx-tests issues --type crash --group-by message` lumps all crashes under "(no message recorded)".
- Workaround: `fx-tests crashes --signature ... --json` has `rows[0].tests[]` with names and counts; printed it with a python one-liner.
- Could show: a `--tests` flag on `crashes` (like `failures --tests`), or listing the tests automatically when a single signature matches.

## `fx-tests test --bugs` printed nothing about bugs

- Command: `fx-tests test browser/components/backup/tests/xpcshell/test_BackupService_archive.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`; no line saying none was found. (A generic bug, 2061660 "Intermittent Storage connection not closed: places.sqlite", exists but does not name the test, so "none" was probably right; still, silence reads like the flag was ignored.)
- Workaround: Bugzilla quicksearch via curl.
