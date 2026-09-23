## Question: which failure message did each config / task of a try push produce?

- Command: `fx-tests try <rev> --profiles --task-ids --limit 0` (and `--messages`).
- Expected: the messages broken down by config, since this test fails two different ways (a11y-checks on Linux, a wrong event order on Windows).
- Got: one merged message list with counts, and a task list with no message attached; the split by config had to be inferred from the counts, then confirmed with `fx-tests task <id>` on one task per config.
- Also: without `--limit 0` the test was not in the output at all (the perma-fail list is truncated with "… 25 more"), so a first grep for the path found nothing.
- What the output could show: the config(s) next to each message.
