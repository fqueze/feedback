## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_03.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, no bug section at all, so I could not tell "no bug" from "flag ignored" without re-running and diffing.
- Workaround: assumed none; found the msix bug (1991833) through a sibling report.

## Question: "this test's outcome and profile in one job", in a job with hundreds of failures

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (then `--limit 0` and grep for the test)
- Expected: a way to ask for one test's row, e.g. `fx-tests task <id> --test <path>`.
- Got: the first ~13 of 488 failures; my test was not in them. `--limit 0` gives 2,000-6,000 lines per job to grep.
- Workaround: `--limit 0 > file; grep -A4 <test>`.
