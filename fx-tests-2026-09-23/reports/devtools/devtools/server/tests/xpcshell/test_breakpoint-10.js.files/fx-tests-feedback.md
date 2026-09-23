## Question: how did my test fail in this job? (fx-tests task)

- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles`
- Expected: a way to see the row for `devtools/server/tests/xpcshell/test_breakpoint-10.js` in a job with 477 failures.
- Got: the default list is truncated and sorted by path, so the test is not shown; there is no `--test <path>` filter.
- Workaround: `--limit 0` into a file, then `rg`. A `--test <path>` (or substring) filter would answer it directly.

## Question: are these TIMEOUTs launch failures? (fx-tests task)

- Command: same as above.
- Expected: some hint that 470 of the 477 TIMEOUTs lasted exactly the kill interval (45.0 s) and started within 1.5 s of each other, i.e. the job broke rather than 470 tests hanging.
- Got: 477 rows each saying `TIMEOUT — 1 failing execution of 1`. The start/duration clustering is only visible from the resource-usage profile's `test` markers.
