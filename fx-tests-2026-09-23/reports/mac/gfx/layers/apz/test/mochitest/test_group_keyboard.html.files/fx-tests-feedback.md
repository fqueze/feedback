## Question: which failing task showed which failure message?

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_keyboard.html --task-ids --limit 0`
- Expected: each task ID next to the message(s) it failed with, so that two failure modes (equal offsets vs. a backward jump) can be told apart and a profile picked for each.
- Got: an "Issues" list collapsed as "↑ same as 1, but ...", followed by a separate date-grouped task list with no messages. The collapse also hides that issue 2 ("nondecreasing ... 422.8 → 268.2", a backward jump) is a different failure mode from issue 1 ("strictly ... X → X", a repeated offset): they share a prefix only.
- Workaround: a shell loop over `fx-tests task <id> --profiles --messages` for all 14 tasks.
- What would have answered it: `--task-ids` printing the task IDs under each issue (or the messages under each task).
