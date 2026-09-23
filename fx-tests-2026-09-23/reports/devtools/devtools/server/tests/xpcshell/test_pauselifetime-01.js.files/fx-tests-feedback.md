# fx-tests feedback (test_pauselifetime-01.js)

## Question: this one test's outcome and profile in a given job

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to ask for one test's row in the job (e.g. `--test <path>`), since I came from `fx-tests test <path> --task-ids`.
- Got: the first ~20 of 488 failing tests, alphabetically, then `… 468 more (--limit 0 for all)`. My test was not among them.
- Workaround: `COLUMNS=400 fx-tests task <id> --profiles --limit 0 --full-messages --messages > file`, then `rg -A6 <test>` on the file, six times (once per job). About 1,600 lines per job to find 3.
- Could have shown: with `--test`, just that test's row, and whether it had a per-test profile (here it had none, which I only learned by grepping for `profile_` near it).
