## Question: the task IDs and per-test profile URLs of one test's failures on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test accessible/tests/mochitest/events/test_selection.html --task-ids` (also with `--json`)
- Expected: the per-config ran/pass/fail table plus the failing task IDs (and, with `--profiles`, the per-test profile URLs), since `--task-ids` was accepted.
- Got: only the per-config counts (2 configs, 4 jobs failed); `--task-ids` silently had no effect, and the JSON has no task field either.
- Workaround: re-ran the whole push report with `--all-jobs --profiles --task-ids --limit 0 --full-messages` (45 s, 800+ lines) to a file and searched it for the test's row. In that row the task list still truncates ("… 3 more tasks") even under `--limit 0`, while the profile URL list below it is complete, so the profile lines were the only full list of tasks.
