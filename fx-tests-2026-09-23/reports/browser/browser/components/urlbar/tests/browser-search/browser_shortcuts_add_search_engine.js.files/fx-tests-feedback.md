## When did a failure stop on some configs?

- Question: which push was the last one to fail on non-standalone configs (the failure dropped from ~100/day to 0 after 2026-09-10)?
- Commands: `fx-tests test <path> --day 2026-09-10 --task-ids --limit 0 --exclude-config standalone`, then `fx-tests task <id>` for each of 70 task IDs to get its revision, then lando hg2git + `git log` to order them by push time.
- Got: task IDs without revision or push time; `fx-tests task` prints only a 12-char revision on its second line (the full hash is only inside the Treeherder URL, and lando's hg2git needs the full one).
- What could have shown it: `--task-ids` printing the push time and full revision per task, or `--history` accepting `--by push` for one day, so the last failing push and the first clean one are visible directly.
