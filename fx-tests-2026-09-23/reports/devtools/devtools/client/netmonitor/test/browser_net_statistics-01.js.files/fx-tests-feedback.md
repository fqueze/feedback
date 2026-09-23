## `--task-ids` ignored with `try --all-jobs --test`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/netmonitor/test/browser_net_statistics-01.js --task-ids`
- Question: which task IDs ran this skipped-then-unskipped test and passed, so I can open their resource-usage profiles.
- Expected: the per-config table plus the task IDs behind each row.
- Got: only the per-config counts (jobs / passed / passed on retry / failed); no task IDs, no error about the flag.
- Workaround: borrowed the task IDs from a sibling report's scratch files, then confirmed each with `fx-tests task <id> --passed --limit 0`.

## `test --bugs` finds nothing for a test named in a bug summary

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_statistics-01.js --bugs`
- Question: which bug tracks this test's failures.
- Expected: bug 2026850, "Intermittent devtools/client/netmonitor/test/browser_net_statistics-01.js | single tracking bug" (RESOLVED FIXED by the skip patch), named in the manifest's `skip-if` comment.
- Got: the same output as without `--bugs`, with no bug section and no "no bugs found" line.
- Workaround: took the bug number from the removed `skip-if` comment in the manifest.
