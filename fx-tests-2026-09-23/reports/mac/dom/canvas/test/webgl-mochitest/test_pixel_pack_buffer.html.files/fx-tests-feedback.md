## The task IDs and profile URLs of one test's failures on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --harness mochitest --test dom/canvas/test/webgl-mochitest/test_pixel_pack_buffer.html --task-ids --profiles --limit 0`
- Expected: the per-config table, followed by the failing task IDs and the per-test profile URLs of this one test (the brief says to use `--profiles --task-ids` for a skipped test's try push).
- Got: only the 7-line per-config ran/failed table; `--task-ids` and `--profiles` are silently ignored when `--test` is given.
- Workaround: ran the whole-push listing (`fx-tests try <rev> --harness mochitest --profiles --task-ids --limit 0`, ~800 lines for this push) and grepped for the test path.
- Also: in that whole-push listing, the task list under the test still ends with `… 3 more tasks` despite `--limit 0`, while the profile URLs below it list all 4 jobs.
