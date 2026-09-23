## Question: "how did my test fail in this job?" (fx-tests task)
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to see the row for one test (`--test <path>`), or the test being investigated shown first.
- Got: the FAILED list is truncated after about 20 of 488 rows, and my test was not among them. I had to rerun it for all 6 jobs with `--limit 0` and grep.
- Workaround: `COLUMNS=300 fx-tests task <id> --profiles --limit 0 | grep -A3 <test>`.
