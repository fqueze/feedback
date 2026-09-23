## Question: "what was the first failure of each execution (first run and retry) of this test on the try push?"

- Commands: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --messages --task-ids`, then `fx-tests task <taskId>` for each of the 8 tasks.
- Expected: per execution (task, first run / retry), the first failure message and the subtest it ended in. For this test the modes split cleanly by build type (`[multiple]` first in all 8 opt executions, `Test timed out` or `[selector]` in the debug ones), which is the main fact the triage needs.
- Got: `try --messages` gives counts pooled over all 16 executions (`8x [multiple] ...`, `3x [rollback] ...`), with no link to tasks or runs. `task` gives `messages: ["Test had failures, will retry", "Test timed out"]` for a debug task and `allMessages: []`, which says nothing about the first run's failures. The errorsummary artifact only holds the retry's lines.
- Workaround: downloaded all 8 `live_backing.log`s and grepped for `TEST-FAIL` / `TEST-UNEXPECTED-*` between the two `TEST-START`s.
- What would have answered it: in `fx-tests task`, one line per execution with its first failing message (the first run's `TEST-FAIL` lines included), e.g. `run 1: [multiple] Both windows ... ; run 2 (retry): Test timed out`.

## `--limit 0` does not expand the per-test task and profile lists in `fx-tests try`

- Command: `COLUMNS=250 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: all 16 task rows and 8 test-profile URLs for `test_fullscreen-api.html`.
- Got: still `… 11 more tasks` and `… 3 more profiles`.
- Workaround: `--json` and read `permaFails[].taskIds` / `.profiles`.

