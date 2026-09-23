## Question: the task IDs and profile URLs of one test's failures on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --harness mochitest --test accessible/tests/browser/tree/browser_select.js --profiles --task-ids --full-messages --messages`
- Expected: the per-config table plus the failing task IDs and per-test profile URLs for that one test.
- Got: only the per-config table; `--profiles`, `--task-ids` and `--messages` were silently ignored in `--test` mode (no warning).
- Workaround: ran the whole push without `--test` and `--limit 0`, wrote it to a file and grepped for the test path (1,255 lines in). Cost one slow extra run.
