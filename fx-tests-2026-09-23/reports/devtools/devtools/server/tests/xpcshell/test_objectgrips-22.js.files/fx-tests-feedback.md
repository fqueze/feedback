## `fx-tests test` does not say that every failure came from a job where hundreds of other tests failed too (question)

- Question: is this test's failure its own, or collateral of a job-wide meltdown?
- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-22.js`. It reported "6x TIMEOUT Test exceeded time limit", 1.8% on msix, and `--bugs` found nothing.
- Answer found by: running `fx-tests task <id>` on each of the 6 tasks. Each showed 476 to 1563 TIMEOUTs in the job, all behind bug 1991833, which names no test.
- What could show it: in `test`, the number of other tests failing in each failing job ("6 of 6 failing jobs had >400 other failures"). With `--bugs`, the bugs those jobs were annotated with, even when the bug names no test.
- Related: these TIMEOUTs are logged with `expected=TIMEOUT` (parallel phase, retry pending), and the retry never ran. Counting them as failures of the test inflates every test queued after the break.
