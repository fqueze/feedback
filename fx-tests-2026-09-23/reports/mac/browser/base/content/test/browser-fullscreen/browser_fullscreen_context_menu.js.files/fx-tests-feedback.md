## The task IDs of the jobs where a test passed on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --test browser/base/content/test/browser-fullscreen/browser_fullscreen_context_menu.js --task-ids` (and the same with `--json`)
- Expected: the task ID of each job counted in the ran/passed/failed table, so the passing run can be opened (`fx-tests task <id> --profiles`) and compared with the failing one.
- Got: only per-config counts (`debug-mochitest-browser-chrome-11  2 jobs, 1 passed, 1 failed`); `--task-ids` is ignored with `--test`, and the JSON has no task IDs either.
- Workaround: Treeherder API (`/api/project/try/push/?revision=` then `/api/jobs/?push_id=`), filtered on job_type_name.
- What it could have shown: one task ID per job under each config row, as `--task-ids` does for failures.
