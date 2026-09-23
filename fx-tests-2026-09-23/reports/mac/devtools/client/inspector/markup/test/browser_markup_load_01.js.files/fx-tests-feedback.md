## `fx-tests try <rev> --test <path> --task-ids --profiles` prints neither task IDs nor profiles

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test devtools/client/inspector/markup/test/browser_markup_load_01.js --profiles --task-ids --full-messages`
- Expected: the per-config table plus the failing task IDs and profile URLs for this one test.
- Got: only the per-config table (jobs/failed/passed); `--task-ids` and `--profiles` silently ignored.
- Workaround: `fx-tests try <rev> --harness mochitest --profiles --task-ids --messages --full-messages --limit 0 > file`, then grep the 2,851-line output for the test. Its block then shows `… 3 more tasks` and only one of the two profiles per task (not the `-2` retry), so `fx-tests task <id> --profiles` per task was still needed.
