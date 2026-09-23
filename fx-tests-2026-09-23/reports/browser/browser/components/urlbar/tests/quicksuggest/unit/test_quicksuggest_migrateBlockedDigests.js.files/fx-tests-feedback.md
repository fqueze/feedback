## `fx-tests test <path> --bugs` prints nothing when there is no bug
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrateBlockedDigests.js --bugs`
- Expected: a line such as "Annotated bugs: none".
- Got: the same output as without `--bugs`, so "no bug" looks like "flag ignored".
- Workaround: `--json` and read `annotatedBugs: []`.

## Question: is this failure specific to this test, or one of a mass event in the job?
- `fx-tests test` shows 59 CRASH `child process hang at shutdown` on macOS debug, but only `fx-tests task <id>` per job reveals that each failing job has 50-120 tests crashing the same way, and `fx-tests crashes` that the signature spans 503 tests. A per-test hint like "in the failing jobs, N other tests failed with the same signature (median)" would have answered it at the first command.
