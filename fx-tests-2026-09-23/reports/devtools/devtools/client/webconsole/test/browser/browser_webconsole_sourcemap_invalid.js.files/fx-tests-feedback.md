# fx-tests feedback (browser_webconsole_sourcemap_invalid.js)

## `try --project autoland`: "Pre-existing" counts the push's own failures as central history

- Command: `fx-tests try 82333bc598c6 --project autoland --harness mochitest --perma-only --task-ids`
- Expected: for a push whose failures are the only ones in the window, a verdict that the failure is new with this push.
- Got: "Pre-existing: central already fails the same way on the 3 configs it failed every run on (6 times in 21 days) — probably not yours." The 6 "central" failures are the tasks ebemDBR_Q8OlanFHpSc8Mw, ImW2ftKzRfW-gf6vi7uOWQ and bUHFNYUQRd2iYTxms08_Qg, all on this very autoland push (82333bc598c6), which was then backed out. The verdict points away from the culprit.
- Workaround: `fx-tests test <path> --task-ids` and checking the revision of each failing task by hand.

## `try`: the task list of one row stays truncated with `--limit 0`

- Question: every task ID behind one test's failures on one push.
- Command: `fx-tests try 82333bc598c6 --project autoland --harness mochitest --perma-only --task-ids --limit 0`
- Expected: all 17 job runs' task IDs for the row.
- Got: 5 task lines, then "… 29 more tasks". `--config` is refused on `try`, so no way to narrow to the Windows ones either.
- Workaround: `--json` and reading `permaFails[].taskIds`.
