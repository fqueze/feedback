## `fx-tests task` does not show the row for the test I came from

- Question: "did my test fail in this job, and how?"
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: the row for `devtools/server/tests/xpcshell/test_conditional_breakpoint-01.js`, the test whose `--task-ids` gave me this job.
- Got: the first 20 failing tests alphabetically (browser/components/backup/...), then `… 468 more`.
- Workaround: `--limit 0` (1,969 lines) into a file, then `rg`.
- Would help: `fx-tests task <id> --test <path>`, or `fx-tests test <path> --task-ids` printing each job's row for that test inline.
