## `fx-tests task` drops a test's harness ERROR in one job out of six

- Command: `COLUMNS=300 fx-tests task FADMjPT5QXm-7tdUJVLRRQ --limit 0 --messages --quiet`
- Expected: for `devtools/server/tests/xpcshell/test_stepping-17.js`, the same message as in the other five jobs: `... | Timed out and was force-killed by the harness; ...`. The resource-usage profile of FADMjP has that ERROR marker for this test at t=97.22 s, inside the test's replayed log.
- Got: only `Test timed out` (the `test` marker's message), even with `--messages`. The other five jobs show the force-killed ERROR.
- Cost: I loaded the profile to check whether FADMjP was a different failure mode. It was not.
- Workaround: `profiler-cli thread markers --search <test> --list`.

## Question: "what happened to test X in job Y"

- Command: `fx-tests task <taskId> --profiles --limit 0 > file; grep -A3 <test> file`, run for 6 jobs of 476 to 1,563 failing tests each.
- The default output is capped at 20 failures, and there is no `--test <path>` filter. The first run looked like the test was absent from the job, until I noticed the `… 468 more` line.
- What would answer it: `fx-tests task <taskId> --test <path>`, printing that test's executions, messages, per-test profile URL and phase (parallel or retry).
