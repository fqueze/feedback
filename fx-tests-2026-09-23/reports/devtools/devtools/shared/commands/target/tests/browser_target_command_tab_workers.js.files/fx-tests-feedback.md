## `--since` takes a day count, not a date
- Command: `fx-tests test devtools/shared/commands/target/tests/browser_target_command_tab_workers.js --since 2026-09-12`
- Expected: the window starting on that date (the history had just shown the last failure on 2026-09-11).
- Got: `--since expects a non-negative integer, got "2026-09-12"`.
- Workaround: worked out the day count by hand (`--since 9`).

## Question: were the last failures before or after a given landing?
- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --profiles` for each of the last few tasks, then `curl hg.mozilla.org/.../json-pushes?changeset=<rev>` to get push times.
- The `--task-ids` list gives only the day for each task. Showing the push revision and push time next to each task ID would answer "did any failure happen after revision X landed?" in one command.
