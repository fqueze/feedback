## Question: the task IDs of the jobs where one test passed (skip-if removal check)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/inspector/animation/test/browser_animation_logic_mutations_fast.js --all-jobs --task-ids` (also with `--json`)
- Expected: the task IDs behind each config's `passed` count, so I can open one passing job's resource-usage profile.
- Got: per-config counts only; `--task-ids` printed nothing and the JSON has no task IDs when nothing failed.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=`, then `/api/jobs/?push_id=`) filtered on the job names by hand.

## Question: how long this test took in one job

- Command: `fx-tests task JeK-VBYYRBul1tGi4v945w --passed --limit 0` (and `--json`)
- Expected: per-test duration next to `PASS`, to judge headroom against the timeout on slow configs.
- Got: outcome and execution count only (`{'path': ..., 'executionCount': 1}` in JSON).
- Workaround: loaded the resource-usage profile in profiler-cli and read the `test` marker's duration.

## `jobs` count differs between `try --test` with and without `--all-jobs`

- Command: `fx-tests try 2888bcab0070 --test <path>` shows `jobs 4` for linux asan chrome-11 and linux debug chrome-7; the same with `--all-jobs` shows `jobs 3`.
- Cause found: each config had one infra-retried run (`.0`, Treeherder result `retry`, no resource-usage profile). The first view counts it, the second does not, and neither says so.
- Expected: one count, or a note such as "1 run infra-retried, no profile".
