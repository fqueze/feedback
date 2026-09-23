## Question: the failing tasks and profile URLs of one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/responsive/test/browser/browser_touch_all_events_long_tap.js --task-ids --profiles`
- Expected: the per-config ran/pass/fail table, plus the task IDs and per-test profile URLs of the failing runs, since `--task-ids --profiles` were given.
- Got: only the per-config table; `--task-ids` and `--profiles` were silently ignored with `--test`.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0` for the whole push (1455 lines) and search it for the test path, then `--json` to get all 24 task IDs, since the text row stops at 5 tasks ("… 19 more tasks") and 5 profiles with no flag to show them all.
- What would have answered it: `--test <path>` honouring `--task-ids --profiles`, listing each failing task with its config and both per-test profile URLs (`-2` included).
