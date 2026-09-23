## One test's row in a job with 750+ failures (question: "what happened to test X in this job?")

- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ --profiles`
- Expected: a way to see the row of the test I am investigating.
- Got: the first 20 of 755 failures, `… 735 more (--limit 0 for all)`; my test was not among them. Had to rerun with `--limit 0` and grep 3,000+ lines, for each of the 3 jobs.
- Workaround: `--limit 0 | grep -A3 test_fetch-file.js`. A `--test <path>` filter on `task` would answer it directly. The output could also say the job collapsed (e.g. "750 TIMEOUTs, all starting within 10 s of each other"), which is what matters for every test in it.
