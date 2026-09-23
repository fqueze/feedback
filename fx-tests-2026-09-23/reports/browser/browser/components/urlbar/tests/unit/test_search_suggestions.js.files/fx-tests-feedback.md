## Question: the failing tasks of one platform (here: the non-macOS-15 ones)

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: a way to restrict the task list to one config, or the list grouped by config.
- Got: 1321 task ids by day; the Windows ones are buried (`grep windows` on the text output found 2 of 12 lines because the rest were beyond a pager-sized read). `--config` exists on `test`? not tried first.
- Workaround: `--json` and a python filter on `jobName`.
- What could have shown it: `--task-ids` grouped by config, or a per-config sample of task ids under the config table.
- Correction to the entry above: the 2-of-12 was my own `| head` cutting the output after the config table rows, not the tool. The real gap is only that `--task-ids` cannot be narrowed to, or grouped by, a config in text output.
