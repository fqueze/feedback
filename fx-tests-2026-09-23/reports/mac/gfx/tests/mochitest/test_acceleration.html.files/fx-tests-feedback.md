## Question: the failure message, task IDs and profiles of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --harness mochitest --test gfx/tests/mochitest/test_acceleration.html --profiles --task-ids --full-messages --messages`
- Expected: the per-config table plus the test's failure messages, task IDs and profile URLs, since those flags were passed.
- Got: only the per-config ran/failed table; `--profiles`, `--task-ids` and `--messages` were silently ignored with `--test`. Adding `--config plain-gpu` to narrow the full report instead is refused (by design, but it leaves no narrow way in).
- Workaround: ran the whole push report (`--profiles --task-ids --full-messages --messages --limit 0`) into a file and grepped for the test's block.
- Also: in that block, with `--limit 0`, the task list still stops at 5 with `… 3 more tasks`, so the retry-run task IDs of the last jobs needed `fx-tests task`.
