## The task IDs and profile URLs of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test toolkit/crashreporter/test/unit/test_crashreporter_appmem.js --task-ids --profiles --messages --full-messages`
- Expected: the per-config table, plus the failing task IDs, the failure messages and the per-test profile URLs for that one test, since `--task-ids`, `--profiles` and `--messages` were passed.
- Got: only the per-config table (jobs / failed / passed on retry / passed). The three flags were silently ignored.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0 > file` (2778 lines for this push) and searching the file for the test path. Without `--limit 0` the test was not in the output at all: it was row 31 of 51 perma-fails, and the default shows 10.
- What would have answered it: `--test` honouring `--task-ids` / `--profiles` / `--messages`, printing the same block the push-wide view prints for that test's row.
