## Question: which task IDs ran this test on the config where it failed?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/test/browser_inspector_textbox-menu.js --task-ids`
- Expected: the per-config table plus the task IDs behind each row (at least behind the `failed` column), since `--task-ids` was accepted.
- Got: the table only (`a11y-checks-4 ... 3 jobs, 0 passed, 3 failed`), no task IDs; `--json` of the same command has no task IDs either.
- Workaround: ran `fx-tests try <rev> --all-jobs --task-ids --json` over the whole push (a second full read of 234 job profiles) and walked the JSON for `taskIds` entries whose jobName contained `a11y-checks-4`. The test was in `permaFails` there, but the text output had cut it off behind `… 25 more`.
- What could have shown it: `--test` honouring `--task-ids`/`--profiles`, printing the failing tasks and their per-test profile URLs under the row.
