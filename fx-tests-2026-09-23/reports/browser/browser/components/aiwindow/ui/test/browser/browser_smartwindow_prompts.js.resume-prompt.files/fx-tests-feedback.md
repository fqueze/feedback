## "Which configs does this one failure mode hit?" — per-config counts per issue

- Command: `fx-tests test <path> --task-ids --issue 4 --limit 0`
- Question: how the three messages of one subtest spread over configs (Windows opt vs debug builds).
- Got: task IDs listed per day with the job name; the per-config table above is for all failures of the test, not the issue.
- Workaround: awk over the job names, stripping the chunk number.
- What the output could have shown: a per-config count table under `--issue <n>` (the same table as the test's, restricted to that issue).

## `fx-tests task` has no way to restrict to one test

- Command: `fx-tests task <id> --messages --test browser_smartwindow_prompts.js` → `unknown option --test`.
- Workaround: `sed -n '/browser_smartwindow_prompts/,/^$/p'` on the full output.
