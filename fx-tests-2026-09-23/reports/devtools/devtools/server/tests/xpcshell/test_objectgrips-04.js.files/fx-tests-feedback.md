## `fx-tests task` hides the one test I asked about, and does not flag a job-wide breakdown

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: the row for the test I came from (`devtools/server/tests/xpcshell/test_objectgrips-04.js`), and, since 487 of 550 tests timed out, a line saying the job broke down as a whole and from when (the first failure's time and the tests running then).
- Got: the first ~17 failing tests alphabetically, then `… 468 more`. Nothing says the job collapsed.
- Workaround: `--limit 0` (1,969 lines) and grep; then profiler-cli plus a script to find when the collapse started.
- Would help: `fx-tests task <id> --test <path>` to show one test's row; and a "job-wide: N of M tests TIMEOUT, starting at t=…" summary line when most of a job fails.
