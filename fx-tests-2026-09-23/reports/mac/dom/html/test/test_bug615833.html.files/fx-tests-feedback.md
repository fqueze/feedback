## The failing task IDs and profile URLs of one test on a try push

- Question: every failing job (task ID + config) and every per-test profile URL for one test on a try push.
- Command: `COLUMNS=250 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: `--limit 0` to expand the per-test lists.
- Got: each test row still ends with `… 11 more tasks` and `… 3 more profiles`; `--limit 0` only lifts the number of test rows. The retry profiles (`profile_<test>-2.html.json`) are not listed at all, only the first-run one per task.
- Workaround: `--json` and a Python filter over `permaFails[].taskIds` / `profiles`, then `fx-tests task <id> --profiles` per job to find the `-2` retry profile.
- What the default output could show: with `--limit 0`, or with a `--test <path>` filter, the full task list and both profiles per task.
