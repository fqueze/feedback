## Question: on which revision did each failing task run?

- Command: `fx-tests test <path> --task-ids --limit 0` (to see whether failures stopped at a given landing).
- Expected: the repo and revision next to each task ID, since "did it fail after the fix landed?" is the first question once `--history` shows the failures stopping.
- Got: task ID, job name and day only (also in `--json`: no revision field).
- Workaround: ran `fx-tests task <id>` per task to read its revision line.
