## Question: "which tests are behind this message?"

- Command: `fx-tests failures --path devtools/client/storage/test --harness mochitest --limit 30`
- Expected: for a message counted in several tests, the tests' names (at least the top ones).
- Got: only `failures  tests  message` columns: `44  2  The row id 'ss1' that was passed to getRowCells() does not exist.` Which 2 tests needed `--json` (the `tests` array was there).
- Could have shown: the test paths under each row with more than one test, or a `--tests` flag.

## `failures` rejects a positional path

- Command: `fx-tests failures devtools/client/storage/test --harness mochitest`
- Got: "failures takes no positional arguments"; `test`, `try --test` and friends take a path. Minor, and the error suggested `--path`.
