## `fx-tests try --test` ignores `--task-ids`

Question: which task IDs ran this test on the try push, so I can open their resource-usage profiles and check it actually ran?

Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/responsive/test/browser/browser_touch_event_iframes.js --task-ids` (and `--json`)

Expected: the per-config table with the task ID(s) of each job run that ran the test (and ideally their resource-usage profile URLs with `--profiles`).

Got: only the counts table (jobs / passed / passed on retry / failed). No task IDs in text or `--json`.

Workaround: queried the Treeherder REST API (`/api/project/try/push/?revision=...`, then `/api/jobs/?push_id=...`) for the Windows job task IDs, then `fx-tests task <id> --passed --limit 0` on each to confirm the test ran and passed.
