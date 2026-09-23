## The jobs behind one message, tree-wide

Question: which jobs produced the 744 `NS_ERROR_FILE_CORRUPTED ... readUserPrefsFromFile` failures in 372 tests?

- Command: `fx-tests failures --harness xpcshell --message NS_ERROR_FILE_CORRUPTED --json --limit 0`
- Expected: the task IDs (or at least the job count / configs) behind the message; a message where every test fails exactly twice is the signature of a few whole jobs breaking.
- Got: tests and counts only ("This file records no job names"). I had to run `fx-tests test <t> --task-ids --issue <n>` on sample tests, then `fx-tests task <id> --limit 0` on each job found, and diff the test sets in a script to find the 13 tests belonging to a third job (TNpgxvmV). About 10 commands.
- Could have shown: "from N jobs" with task IDs, or `fx-tests failures --message X --task-ids`.
