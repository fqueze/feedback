## "Which revisions did the last failures of a config happen on?" (to bound when a failure stopped)

- Command: `fx-tests test <path> --task-ids --limit 0 --day 2026-09-10 --exclude-config standalone`, then one `fx-tests task <id>` per task only to read its second line (`autoland cf7bf384b191`)
- Expected: the repo and revision next to each task ID in `--task-ids` (text and `--json`), since `task` already knows it
- Got: task IDs and job names only; `--json` has no revision either, so bounding the stop meant 20 extra `fx-tests task` calls
- Workaround: loop over `fx-tests task <id> --quiet | sed -n 2p`
- What the output could have shown: `rev` and `repo` per task, or a `--by-revision` view listing the last failing and first passing push per config

