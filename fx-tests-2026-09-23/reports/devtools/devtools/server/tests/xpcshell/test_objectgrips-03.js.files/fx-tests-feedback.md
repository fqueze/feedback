## Question: "what did this one test do in this job" (fx-tests task)

- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles`
- Expected: a way to see the row for one test (`devtools/server/tests/xpcshell/test_objectgrips-03.js`) in a job with 477 failures.
- Got: the first 20 rows and `… 457 more (--limit 0 for all)`; no option to restrict to one test path.
- Workaround: `--limit 0` into a file (1928 lines), then grep. A `--test <path>` (or `--search`) filter on `fx-tests task` would answer it directly.
