## "How did my test fare in this job?" has no direct answer in `fx-tests task`

- Command: `COLUMNS=250 fx-tests task <taskId> --profiles | grep conditional_breakpoint-02` for each of the 6 msix jobs.
- Expected: a way to ask for one test's outcome, message and profiles in a job, e.g. `fx-tests task <taskId> --test <path>`.
- Got: nothing, because FAILED is truncated to 20 entries (`… 1041 more (--limit 0 for all)`), and in these jobs the test is one of 476-1563 TIMEOUTs.
- Workaround: `--limit 0` into a file (1,000-4,700 lines per job), then grep.
