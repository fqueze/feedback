# fx-tests feedback — test_click_hold_context_menus.html

## Question: the task IDs and profile URLs of every failure of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --harness mochitest --task-ids --profiles --messages --limit 0 --full-messages`
- Expected: all 8 task IDs and all per-test profile URLs (first run and `-2` retry) for `dom/events/test/test_click_hold_context_menus.html`.
- Got: the first 5 task rows, then `… 11 more tasks`, and 5 profile pairs, then `… 3 more profiles`, even with `--limit 0` (which seems to apply to rows, not to the per-row task/profile lists). The per-row lists also list each task twice (once per failing execution) and give only the first-run profile, never the `-2` retry one.
- Also: `--test <path>` together with `--task-ids --profiles` printed only the per-config table, with no task IDs or profiles at all.
- Workaround: `--json` and a Python script over `permaFails[].taskIds` / `permaFails[].profiles`, then `fx-tests task <taskId> --profiles` to learn the `-2` retry profile URL.
- What the output could have shown: with `--limit 0`, every task (deduplicated, one line per task with its job name), each with both of its profile URLs; and `--test` honouring `--task-ids --profiles`.
