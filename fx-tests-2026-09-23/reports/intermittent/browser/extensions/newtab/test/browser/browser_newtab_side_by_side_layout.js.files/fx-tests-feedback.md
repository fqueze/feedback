## `--since` takes a day count, but the guide says "--day and --since filter a file"

- Command: `fx-tests test <path> --task-ids --issue 1 --limit 0 --since 2026-09-17`
- Expected: the task IDs from 2026-09-17 on (the guide's THE WINDOW section mentions `--day and --since` next to dates, and the output groups task IDs by date).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-17"`. Its exit status was 0 through the pipe, and I missed it with `| sed`.
- Workaround: `sed -n '/2026-09-17/,$p'` on the full list.

## Question: "did any failure happen on a revision that contains fix X?"

- Needed when a fix landed on the last day of the window. `fx-tests task <id>` prints one revision per task, so answering meant one `fx-tests task` call per task, then lando hg2git plus `git merge-base --is-ancestor`, for 15 tasks.
- What could show it: the push time or revision next to each task ID in `fx-tests test --task-ids`, or a `--after-rev <rev>` filter.
