## `fx-tests task` cannot be narrowed to one test

- Command: `fx-tests task <taskId> --profiles` on six msix jobs with 476 to 1,563 failing tests each.
- Question: this one test's outcome and message in each of those jobs.
- Expected: something like `--test <path>` to print only that row.
- Got: the first ~20 rows only; `--limit 0 --full-messages` then `rg -A3 test_stepping-02` over 500-2,000 rows per job.
- Workaround: the grep above.
