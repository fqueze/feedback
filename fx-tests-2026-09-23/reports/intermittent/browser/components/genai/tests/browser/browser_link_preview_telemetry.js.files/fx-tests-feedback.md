## Question: which other tests hung the whole browser the same way ("application timed out after 370.0 seconds with no output")?

- Commands: `fx-tests failures --harness mochitest --message "application timed out after 370" --tests --limit 0` and `... --message "Test exceeded time limit" --since 10`.
- Expected: the tests whose runs ended with that message (`fx-tests task` prints it for this test, `fx-tests test` prints "TIMEOUT Test exceeded time limit" under Issues).
- Got: "No failure matched" for both; the messages `fx-tests test`/`task` show for a TIMEOUT are not in what `failures` searches.
- Also: `fx-tests issues --type timeout --group-by message` printed the same FAIL-dominated table as without `--type` (the `--type` filter seemed ignored with `--group-by message`; it did apply with `--group-by test`).
- Workaround: none; left the "does this trap hit other tests" question to the code (bug 2052772's own marionette/talos fixes).

## `fx-tests test --config a --config b` silently keeps only one

- Command: `fx-tests test <path> --history --config windows11-64-25h2 --config standalone`
- Expected: an AND of both, or an error for a repeated flag.
- Got: counts matching neither restriction alone (217 runs on a day where standalone had 48), no warning.
- Workaround: one `--config` with the full job names comma-separated.
