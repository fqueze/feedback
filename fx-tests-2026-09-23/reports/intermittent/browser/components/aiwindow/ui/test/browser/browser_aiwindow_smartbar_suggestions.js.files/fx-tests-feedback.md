## `--since` rejects a date

- Command: `fx-tests test <path> --task-ids --issue 1 --limit 0 --since 2026-09-21`
- Expected: the failing tasks since that date (the guide says "--day and --since filter a file").
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-21"`.
- Workaround: `--day 2026-09-21`. The error could say what the integer means (days back?) and point to `--day`.

## The first failing push is not shown

- Question: "which push did this start on?" (`--history` shows the step on 2026-09-07).
- Command: `fx-tests test <path> --task-ids --issue 1 --day 2026-09-07`, then `fx-tests task <id>` on each of the 10 tasks to read its revision, then the Treeherder push API for push times.
- The task list could show each task's revision and push time, or `--history` could name the earliest failing push, so the regressor candidate is one command away.
